---
name: doubt-driven-debugging
description: Quy trình 4 bước cô lập và giải quyết lỗi hệ thống phân tán, AI pipeline và bất đồng bộ bằng phương pháp kiểm chứng giả định.
---

# Doubt-Driven Debugging Protocol

Skill này cung cấp quy trình tư duy phản biện (doubt-driven) để xử lý các lỗi phức tạp (race conditions, memory leaks, dropped frames, async deadlocks) mà không đưa ra các bản vá ngẫu nhiên.

---

## 1. Bốn Bước Cô Lập Lỗi (The 4-Step Protocol)

1. **Step 1: Nghi Ngờ Mọi Giả Định (Doubt Assumptions)**
   * Đừng tin vào *"Hàm này chắc chắn trả về đúng format"*.
   * Hãy in ra kiểu dữ liệu thật (`type(x)`), kích thước (`len()`, `shape`), hoặc giá trị thực tế tại runtime.
2. **Step 2: Cô Lập Tối Thiểu (Minimum Reproducible Scope)**
   * Thu hẹp phạm vi lỗi: Lỗi xảy ra ở tầng mạng (Redis), tầng xử lý ảnh (OpenCV/PyTorch), hay tầng hiển thị (Browser)?
   * Tách riêng hàm bị nghi ngờ thành một scratch script độc lập để chạy thử.
3. **Step 3: Kiểm Tra Điểm Biên (Boundary Conditions)**
   * Dữ liệu rỗng (`[]`, `None`, `{}`, empty frame).
   * Kích thước mảng không khớp ($1920 \times 1080$ vs $1280 \times 720$).
   * Timestamp trôi lệch giữa các server/process.
4. **Step 4: Surgical Fix & Verifiable Assertion**
   * Sửa đúng nguyên nhân gốc rễ (Root cause), không che giấu lỗi bằng `try...except: pass`.
   * Thêm `assert` hoặc unit test để đảm bảo lỗi không tái diễn.

---

## 2. Checklist Khi Debug Video Pipeline & Async Backend

| Triệu chứng | Khả năng cao nhất | Cách kiểm chứng |
| :--- | :--- | :--- |
| **FPS sụt giảm nghiêm trọng theo thời gian** | Tràn bộ đệm OpenCV hoặc rò rỉ tensor trên GPU | Kiểm tra `torch.cuda.memory_allocated()` và `cap.set(cv2.CAP_PROP_BUFFERSIZE, 1)`. |
| **Consumer Redis không nhận tin nhắn** | Consumer Group bị treo hoặc message chưa ACK | Chạy `XPENDING` hoặc kiểm tra `XAUTOCLAIM`. |
| **Bounding box lệch vị trí trên UI** | Tỉ lệ scale giữa Canvas và video resolution gốc bị sai | In ra `canvas.getBoundingClientRect()` và `video.videoWidth`. |
| **FastAPI treo không phản hồi** | Chạy hàm sync blocking bên trong async endpoint | Tìm các lệnh `time.sleep` hoặc `requests.get` trong code. |
