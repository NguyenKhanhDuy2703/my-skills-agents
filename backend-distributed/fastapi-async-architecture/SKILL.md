---
name: fastapi-async-architecture
description: Chuẩn thiết kế Async FastAPI: Lifespan context manager, quản lý kết nối DB/Redis, BackgroundTasks và error handling thống nhất.
---

# FastAPI Async Architecture & Clean Routing

Skill này hướng dẫn quy chuẩn xây dựng ứng dụng FastAPI hiện đại với Async I/O, quản lý vòng đời ứng dụng bằng `lifespan` và cấu trúc Dependency Injection sạch sẽ.

---

## 1. Cấu Trúc Khởi Tạo Chuẩn (Lifespan Context Manager)

Không sử dụng các decorator cũ `@app.on_event("startup")` hoặc `@app.on_event("shutdown")`. Sử dụng `lifespan` context manager:

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
import redis.asyncio as aioredis


@asynccontextmanager
async def lifespan(app: FastAPI):
    # --- STARTUP PHASE ---
    print("Khởi động: Kết nối Redis & Database pool...")
    app.state.redis = aioredis.from_url("redis://localhost:6379", decode_responses=True)
    
    yield  # Ứng dụng chạy và tiếp nhận requests
    
    # --- SHUTDOWN PHASE ---
    print("Dọn dẹp: Đóng kết nối Redis & Database pool...")
    await app.state.redis.close()


app = FastAPI(title="SpaceLens Backend API", lifespan=lifespan)
```

---

## 2. Dependency Injection & Error Handling Chuẩn

```python
from typing import Annotated
from fastapi import Depends, HTTPException, Request, status
import redis.asyncio as aioredis


def get_redis_client(request: Request) -> aioredis.Redis:
    """Dependency lấy Redis client từ app state."""
    return request.app.state.redis


RedisDep = Annotated[aioredis.Redis, Depends(get_redis_client)]


@app.get("/api/v1/cameras/{camera_id}/status")
async def get_camera_status(camera_id: str, redis: RedisDep):
    val = await redis.get(f"camera:{camera_id}:status")
    if not val:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"Camera ID '{camera_id}' không tồn tại hoặc chưa hoạt động.",
        )
    return {"camera_id": camera_id, "status": val}
```

---

## 3. Quy Tắc Lập Trình Bắt Buộc

1. **Không chặn Event Loop (Blocking I/O):**
   * Tuyệt đối không dùng `time.sleep()`, các thư viện `requests` synchronous, hoặc `cv2.VideoCapture` đồng bộ trong endpoint `async def`.
   * Sử dụng `asyncio.sleep()` hoặc đẩy hàm blocking sang `asyncio.to_thread(sync_func)`.
2. **Pydantic Schema Validation:** Tất cả Request Body và Response Body phải định nghĩa qua `pydantic.BaseModel` chặt chẽ, không dùng dict thô.
3. **Structured Response:** Trả về mã lỗi HTTP chuẩn RFC (400, 404, 422, 500) kèm trường `detail` rõ nghĩa.
