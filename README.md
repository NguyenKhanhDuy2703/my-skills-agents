# 🧠 Centralized Agent Skills Hub (`my-skills-agents`)

An open, topic-driven collection of **AI Agent Skills** designed under the **`@skills` Protocol** (3-Tier Delivery Model) for modern coding agents (Google Antigravity, Claude Code, Cursor, Codex, AdaL).

> **Core Philosophy:** Attention is a scarce budget. Do not install hundreds of skills permanently into the system prompt. Instead: **Reference on-demand**, **Save what you adapt**, and **Auto-trigger only critical guardrails**.

---

## 📑 Danh mục Skills theo Chủ đề (Topic Directory)

Nhờ cơ chế **"Directory as a Menu"**, bạn có thể gọi một danh mục bất kỳ để xem menu các skill con, hoặc gọi trực tiếp từng skill.

```
my-skills-agents/
├── 👁️ ai-computer-vision/          # Thị giác máy tính & Xử lý Video thời gian thực
│   ├── yolov8-multiprocessing-stream
│   ├── reid-osnet-tracking
│   └── spatial-zone-analytics
│
├── ⚡ backend-distributed/          # Backend phân tán & Hàng đợi Dữ liệu
│   ├── redis-streams-pipeline
│   └── fastapi-async-architecture
│
├── 🎨 frontend-engineering/         # Frontend, Canvas thời gian thực & UI Tokens
│   ├── realtime-canvas-overlay
│   └── tailwind-design-system
│
└── 🛡️ quality-and-workflow/         # Chuẩn mực Code, Debug & Kiểm thử
    ├── karpathy-guidelines
    └── doubt-driven-debugging
```

### 1. 👁️ `ai-computer-vision`
* [`yolov8-multiprocessing-stream`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/ai-computer-vision/yolov8-multiprocessing-stream/SKILL.md) — Kiến trúc xử lý luồng RTSP đa camera song song (multiprocessing), chống nghẽn GIL và tối ưu inference FPS.
* [`reid-osnet-tracking`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/ai-computer-vision/reid-osnet-tracking/SKILL.md) — Re-Identification với OSNet (512-dim embedding) + Cosine similarity duy trì ID khi đối tượng ra vào khung hình.
* [`spatial-zone-analytics`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/ai-computer-vision/spatial-zone-analytics/SKILL.md) — Thuật toán phân tích không gian: Ray-casting Point-in-Polygon cho Tripwire, Dwell Time và Spatial Heatmap matrix.

### 2. ⚡ `backend-distributed`
* [`redis-streams-pipeline`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/backend-distributed/redis-streams-pipeline/SKILL.md) — Mô hình Streaming Producer-Consumer qua Redis Streams (`XADD`, `XREADGROUP`, `XACK`, `XAUTOCLAIM`).
* [`fastapi-async-architecture`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/backend-distributed/fastapi-async-architecture/SKILL.md) — Chuẩn thiết kế Async FastAPI: Lifespan context manager, BackgroundTasks, Dependency Injection & structured errors.

### 3. 🎨 `frontend-engineering`
* [`realtime-canvas-overlay`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/frontend-engineering/realtime-canvas-overlay/SKILL.md) — Kỹ thuật vẽ Bounding Box, Tracking Trail và Heatmap đồng bộ với video Canvas (sử dụng `requestAnimationFrame` và tỉ lệ scale tọa độ).
* [`tailwind-design-system`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/frontend-engineering/tailwind-design-system/SKILL.md) — Hệ thống Design System: Semantic tokens, Modern Dark Theme, Glassmorphism và Micro-interactions.

### 4. 🛡️ `quality-and-workflow`
* [`karpathy-guidelines`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/quality-and-workflow/karpathy-guidelines/SKILL.md) — 4 nguyên tắc vàng giảm thiểu ảo giác: Think Before Coding, Simplicity First, Surgical Changes, Goal-Driven Execution.
* [`doubt-driven-debugging`](file:///d:/Project_NCKH/spacelensproject/my-skills-agents/quality-and-workflow/doubt-driven-debugging/SKILL.md) — Quy trình 4 bước cô lập lỗi hệ thống phân tán và AI pipeline mà không đoán mò.

---

## 🚀 Hướng Dẫn Sử Dụng (The 3-Tier Delivery Model)

### 📌 Tầng 1: Sử Dụng Tức Thì On-Demand (Tier 1 — Reference)
Dùng cho các tác vụ 1 lần hoặc khi cần kiến thức chuyên sâu cho 1 task cụ thể. **Chi phí token: 0 token cố định** (chỉ tải vào ngữ cảnh khi gọi và giải phóng sau phiên).

```text
# Gọi trực tiếp 1 skill từ GitHub
@skills:gh:NguyenKhanhDuy2703/my-skills-agents/ai-computer-vision/yolov8-multiprocessing-stream

# Gọi thư mục để Agent hiển thị Menu lựa chọn
@skills:gh:NguyenKhanhDuy2703/my-skills-agents/backend-distributed
```

---

### 💾 Tầng 2: Lưu & Sở Hữu Trong Dự Án (Tier 2 — Saved / Vendor)
Dùng khi bạn muốn đưa skill vào quản lý trong Git của dự án cụ thể để team cùng chia sẻ hoặc sửa đổi cho hợp với dự án. **Chi phí token: 0 token cố định**.

* **Cách 1 (Bằng hậu tố `:save`):**
  ```text
  @skills:gh:NguyenKhanhDuy2703/my-skills-agents/backend-distributed/redis-streams-pipeline:save
  ```
  *Agent sẽ tự động sao chép skill về `.atskills/` hoặc `.agents/skills/` trong dự án của bạn kèm theo file `.source` đánh dấu nguồn gốc.*

* **Cách 2 (Thủ công):**
  Tải/copy thư mục skill vào thư mục `.agents/skills/<skill-name>/` tại gốc dự án của bạn.

---

### ⚡ Tầng 3: Tự Động Kích Hoạt (Tier 3 — Auto-Trigger / Install)
Chỉ áp dụng cho **những quy chuẩn cốt lõi bắt buộc** (ví dụ: `karpathy-guidelines`). 
Thêm một dòng vào file `.autotrigger` (hoặc `.atskills/.autotrigger`):

```gitignore
# Tự động kích hoạt skill đã lưu nội bộ
quality-and-workflow/karpathy-guidelines

# Hoặc theo dõi trực tiếp từ GitHub repo (luôn cập nhật bản mới nhất)
@gh:NguyenKhanhDuy2703/my-skills-agents/quality-and-workflow/karpathy-guidelines
```

> ⚠️ **Khuyến nghị quan trọng:** Luôn giữ file `.autotrigger` **dưới 10 skills** để đảm bảo khả năng kích hoạt chính xác 100% của AI Agent.

---

### 🗑️ Cách Xóa / Gỡ Bỏ Skill

1. **Hủy tự động kích hoạt:** Xóa dòng tương ứng trong file `.autotrigger`.
2. **Xóa hoàn toàn skill đã lưu trong dự án:** Xóa thư mục của skill đó trong `.agents/skills/<tên-skill>` hoặc `.atskills/<tên-skill>`.

---

## 🛠️ Quy Chuẩn Đóng Góp & Tạo Skill Mới (`SKILL.md`)

Mỗi skill bắt buộc phải đặt trong thư mục riêng và có file `SKILL.md` tuân thủ định dạng chuẩn sau:

```markdown
---
name: ten-skill-ngan-gon
description: Mô tả chính xác 1 câu về nhiệm vụ và khi nào cần kích hoạt skill này.
---

# Tên Skill

## 1. Mục tiêu & Giả định (Goal & Context)
- Nêu rõ bài toán cần giải quyết.

## 2. Nguyên tắc & Quy chuẩn (Core Rules)
- DO: Những điều bắt buộc làm.
- DON'T: Những điều tuyệt đối tránh.

## 3. Quy trình thực hiện (Step-by-Step Instructions)
1. Bước 1...
2. Bước 2...

## 4. Code Mẫu Chuẩn (Production-Ready Code)
```python
# Code mẫu hoàn chỉnh, không dùng mã giả (pseudo-code)
```

## 5. Xử lý sự cố & Kiểm thử (Troubleshooting & Verification)
- Cách kiểm tra kết quả...
```

---

## 📄 Giấy phép & Tác giả
* **Tác giả:** [Nguyen Khanh Duy](https://github.com/NguyenKhanhDuy2703)
* **Giao thức tham chiếu:** `@skills` protocol (SylphAI / UT Austin)
* **Giấy phép:** MIT License
