---
name: karpathy-guidelines
description: 4 nguyên tắc vàng giảm thiểu lỗi và ảo giác của LLM Coding: Think Before Coding, Simplicity First, Surgical Changes, Goal-Driven Execution.
---

# Karpathy-Inspired AI Coding Guidelines

Bộ quy tắc này được thiết kế để định hình hành vi của AI Agent, giúp code luôn chính xác, tối giản và không bị over-engineering.

---

## 1. Think Before Coding (Suy Nghĩ Trước Khi Code)
* **Nêu rõ các giả định:** Nếu có điểm chưa rõ hoặc mơ hồ, hãy hỏi lại thay vì tự đoán mò.
* **Trình bày các phương án:** Nếu có nhiều cách giải quyết, hãy nêu rõ ưu/nhược điểm thay vì âm thầm chọn một cách.
* **Đề xuất cách đơn giản hơn:** Nếu người dùng yêu cầu một giải pháp quá phức tạp trong khi có cách đơn giản hơn, hãy thẳng thắn đề xuất.

## 2. Simplicity First (Ưu Tiên Sự Đơn Giản)
* **Không viết tính năng thừa:** Chỉ giải quyết đúng những gì được yêu cầu trong prompt.
* **Không trừu tượng hóa quá sớm:** Không tạo class/interface/factory phức tạp cho đoạn code chỉ dùng 1 lần.
* **Loại bỏ speculative code:** Không thêm cấu hình dự phòng cho tương lai chưa xảy ra.
* **Refactor độ dài:** Nếu đoạn code có thể viết trong 50 dòng mà đang chiếm 200 dòng, hãy viết lại cho cô đọng.

## 3. Surgical Changes (Chỉnh Sửa Chuẩn Xác Như Phẫu Thuật)
* **Không drive-by refactoring:** Không tự ý sửa formatting, comments, đổi tên hàm ở các file lân cận không liên quan đến bug/task.
* **Tôn trọng style hiện có:** Tuân thủ code style và convention của project hiện tại.
* **Dọn dẹp đúng chỗ:** Chỉ xóa các import/biến mà chính thay đổi của bạn làm cho nó không còn dùng. Không tự ý xóa dead code có sẵn từ trước nếu không được yêu cầu.

## 4. Goal-Driven Execution (Thực Thi Hướng Mục Tiêu)
* Biến yêu cầu thành các mục tiêu có thể kiểm chứng được:
  * Thay vì *"Thêm validation"* $\to$ *"Viết test cho trường hợp input sai, sau đó code để test pass"*.
  * Thay vì *"Sửa lỗi bug X"* $\to$ *"Tạo bước tái hiện lỗi, sau đó sửa code và kiểm tra lỗi đã biến mất"*.
