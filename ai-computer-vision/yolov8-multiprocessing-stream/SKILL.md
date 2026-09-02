---
name: yolov8-multiprocessing-stream
description: Xử lý video RTSP/Webcam đa camera song song bằng YOLOv8 với mô hình Multiprocessing chống nghẽn GIL và sụt giảm FPS.
---

# YOLOv8 Multiprocessing Video Stream Processing

Skill này cung cấp kiến trúc và giải pháp tối ưu cho việc xử lý song song nhiều luồng video (RTSP/file/webcam) bằng YOLOv8 trên Python mà không bị khóa bởi Python GIL (Global Interpreter Lock).

---

## 1. Nguyên Tắc Cốt Lõi (Core Principles)

- **Mỗi Camera 1 Process riêng biệt:** Không chạy nhiều camera trong cùng một Python process hoặc asyncio loop. Sử dụng `multiprocessing.Process` để tận dụng tối đa đa nhân CPU và luồng GPU.
- **Tách biệt Frame Ingestion và Inference:** Luồng đọc frame từ OpenCV (`VideoCapture.read()`) phải chạy ở luồng riêng (hoặc process con) với `queue.Queue(maxsize=2)` để tự động bỏ qua (drop) frame cũ khi inference bị chậm, tránh lag tích lũy.
- **Không truyền numpy arrays qua IPC nặng:** Sử dụng `multiprocessing.Queue` chỉ để truyền metadata kết quả (boxes, classes, confidences, track_ids) thay vì truyền toàn bộ ảnh raw.

---

## 2. Cấu Trúc Worker Chuẩn

```python
import multiprocessing as mp
import time
from typing import Optional
import cv2
import numpy as np
from ultralytics import YOLO


class CameraWorker(mp.Process):
    """Worker độc lập xử lý luồng video cho 1 camera cụ thể."""

    def __init__(
        self,
        camera_id: str,
        rtsp_url: str,
        model_path: str,
        output_queue: mp.Queue,
        stop_event: mp.Event,
        device: str = "0",  # "0" cho GPU hoặc "cpu"
    ):
        super().__init__()
        self.camera_id = camera_id
        self.rtsp_url = rtsp_url
        self.model_path = model_path
        self.output_queue = output_queue
        self.stop_event = stop_event
        self.device = device

    def run(self):
        # 1. Khởi tạo model bên trong process con (tránh chia sẻ CUDA context)
        model = YOLO(self.model_path)
        cap = cv2.VideoCapture(self.rtsp_url)
        cap.set(cv2.CAP_PROP_BUFFERSIZE, 1)

        print(f"[{self.camera_id}] Bắt đầu thu và xử lý luồng...")

        while not self.stop_event.is_set():
            ret, frame = cap.read()
            if not ret:
                time.sleep(0.1)
                continue

            # 2. Chạy tracking/detection
            results = model.track(
                source=frame,
                persist=True,
                verbose=False,
                device=self.device,
                classes=[0],  # Chỉ detect person (hoặc tùy biến)
            )

            detections = []
            if results and results[0].boxes is not None:
                boxes = results[0].boxes
                for box in boxes:
                    xyxy = box.xyxy[0].cpu().numpy().tolist()
                    conf = float(box.conf[0])
                    track_id = int(box.id[0]) if box.id is not None else -1
                    cls = int(box.cls[0])

                    detections.append(
                        {
                            "track_id": track_id,
                            "bbox": xyxy,
                            "confidence": conf,
                            "class_id": cls,
                        }
                    )

            # 3. Đẩy metadata kết quả ra queue
            payload = {
                "camera_id": self.camera_id,
                "timestamp": time.time(),
                "detections": detections,
            }

            if not self.output_queue.full():
                self.output_queue.put(payload)

        cap.release()
        print(f"[{self.camera_id}] Worker đã dừng an toàn.")
```

---

## 3. Checklist Kiểm Tra & Tối Ưu

1. **Khởi tạo CUDA Context:** Tuyệt đối không tạo object `YOLO(...)` trước khi gọi `process.start()`. Hãy khởi tạo model bên trong phương thức `run()`.
2. **Buffer Bloat:** Đặt `cap.set(cv2.CAP_PROP_BUFFERSIZE, 1)` cho RTSP để tránh đọc trễ frame trong quá khứ.
3. **Queue Full Strategy:** Sử dụng `put_nowait()` hoặc kiểm tra `full()` để không làm block worker nếu downstream consumer xử lý chậm.
