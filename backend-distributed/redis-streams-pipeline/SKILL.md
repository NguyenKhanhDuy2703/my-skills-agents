---
name: redis-streams-pipeline
description: Thiết lập kiến trúc Stream Producer-Consumer với Redis Streams, xử lý Consumer Groups, auto-claim pending messages và tối ưu throughput.
---

# Redis Streams Pipeline for High-Throughput Metadata Streaming

Skill này hướng dẫn triển khai hàng đợi phân tán bằng **Redis Streams** (`XADD`, `XREADGROUP`, `XACK`, `XAUTOCLAIM`) để chuyển tiếp metadata thời gian thực từ các AI worker tới Backend và UI mà không gây nghẽn RAM.

---

## 1. Nguyên Tắc Cốt Lõi

- **Tách biệt hoàn toàn Producer và Consumer:** AI Inference service chỉ đẩy metadata vào Redis Stream qua `XADD`. Backend services đăng ký Consumer Group để đọc độc lập.
- **Giới hạn độ dài Stream (`MAXLEN ~`):** Luôn dùng `maxlen=50000, approximate=True` khi `XADD` để Redis tự động dọn dẹp các bản ghi cũ, chống tràn RAM máy chủ.
- **Xử lý Failure với Dead-letter / Auto-claim:** Nếu một consumer bị crash giữa chừng, các message chưa ACK phải được thu hồi tự động qua `XAUTOCLAIM`.

---

## 2. Producer (Python / Async Redis)

```python
import json
import redis.asyncio as redis


class StreamProducer:
    def __init__(self, redis_url: str = "redis://localhost:6379", stream_name: str = "camera:detections"):
        self.redis_url = redis_url
        self.stream_name = stream_name
        self.client: redis.Redis | None = None

    async def connect(self):
        self.client = redis.from_url(self.redis_url, decode_responses=True)

    async def emit_detection(self, camera_id: str, frame_idx: int, detections: list[dict]):
        if not self.client:
            raise RuntimeError("Redis client chưa kết nối.")

        payload = {
            "camera_id": camera_id,
            "frame_idx": frame_idx,
            "data": json.dumps(detections),
        }

        # XADD với giới hạn ~10,000 bản ghi
        msg_id = await self.client.xadd(
            name=self.stream_name,
            fields=payload,
            maxlen=10000,
            approximate=True,
        )
        return msg_id
```

---

## 3. Consumer Group Worker (Async Consumer)

```python
import asyncio
import json
import redis.asyncio as redis


class StreamConsumerWorker:
    def __init__(
        self,
        redis_url: str,
        stream_name: str,
        group_name: str,
        consumer_name: str,
    ):
        self.redis_url = redis_url
        self.stream_name = stream_name
        self.group_name = group_name
        self.consumer_name = consumer_name
        self.client = None

    async def start(self):
        self.client = redis.from_url(self.redis_url, decode_responses=True)
        
        # 1. Tạo consumer group nếu chưa tồn tại
        try:
            await self.client.xgroup_create(self.stream_name, self.group_name, id="0", mkstream=True)
        except redis.ResponseError as e:
            if "BUSYGROUP" not in str(e):
                raise e

        # 2. Vòng lặp đọc stream
        while True:
            try:
                # Đọc tin nhắn mới (id=">")
                messages = await self.client.xreadgroup(
                    groupname=self.group_name,
                    consumername=self.consumer_name,
                    streams={self.stream_name: ">"},
                    count=10,
                    block=1000,
                )

                if messages:
                    for stream, stream_msgs in messages:
                        for msg_id, fields in stream_msgs:
                            await self.process_message(msg_id, fields)
                            # Xác nhận đã xử lý xong
                            await self.client.xack(self.stream_name, self.group_name, msg_id)

                # Thu hồi các message bị treo > 10 giây từ consumer khác bị chết
                claimed = await self.client.xautoclaim(
                    name=self.stream_name,
                    groupname=self.group_name,
                    consumername=self.consumer_name,
                    min_idle_time=10000,
                    start_id="0-0",
                    count=5,
                )
                if claimed and len(claimed) >= 2:
                    for msg_id, fields in claimed[1]:
                        await self.process_message(msg_id, fields)
                        await self.client.xack(self.stream_name, self.group_name, msg_id)

            except Exception as ex:
                print(f"Lỗi Consumer: {ex}")
                await asyncio.sleep(1)

    async def process_message(self, msg_id: str, fields: dict):
        camera_id = fields.get("camera_id")
        data = json.loads(fields.get("data", "[]"))
        # Thực hiện phân tích dữ liệu hoặc chuyển tiếp ra WebSocket
```
