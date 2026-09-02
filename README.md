# 🧠 Centralized Agent Skills Hub (`my-skills-agents`)

Bộ sưu tập kỹ năng quy trình phát triển phần mềm chuẩn mực (**Software Engineering & Development Workflow Skills**) cho AI Coding Agents (Google Antigravity, Claude Code, Cursor, Codex, AdaL), tuân theo **Giao thức `@skills` (Mô hình 3 Tầng — 3-Tier Delivery Model)**.

> **Triết lý cốt lõi:** Ngữ cảnh hệ thống (Context window) là tài nguyên hữu hạn và quý giá. Thay vì cài đặt vĩnh viễn hàng chục skill làm nặng prompt và phân tán sự chú ý của AI, hãy: **Tham chiếu on-demand (0 token)**, **Lưu những gì cần tùy biến** và **Chỉ tự động kích hoạt những quy tắc cốt lõi (< 10 skills)**.

---

## 📑 Danh mục Skills theo Chủ đề (Topic Directory)

Nhờ cơ chế **"Directory as a Menu"**, bạn có thể gọi cả thư mục chủ đề để xem menu hoặc gọi trực tiếp từng skill.

```text
my-skills-agents/
│
├── 📐 architecture-and-specs/          # Thiết kế kiến trúc & Đặc tả
│   ├── spec-driven-development
│   ├── documentation-and-adrs
│   ├── deprecation-and-migration
│   └── context-engineering
│
├── ⚙️ development-workflows/           # Quy trình lập trình & Code Craftsmanship
│   ├── test-driven-development
│   ├── incremental-implementation
│   ├── code-simplification
│   ├── git-workflow-and-versioning
│   ├── interview-me
│   └── karpathy-guidelines
│
├── 🔍 debugging-and-observability/     # Gỡ lỗi & Giám sát hệ thống
│   ├── debugging-and-error-recovery
│   ├── doubt-driven-development
│   ├── observability-and-instrumentation
│   └── performance-optimization
│
├── 🎨 frontend-and-testing/            # Giao diện & Kiểm thử UI / E2E
│   ├── frontend-ui-engineering
│   ├── frontend-design
│   ├── browser-testing-with-devtools
│   ├── webapp-testing
│   └── web-artifacts-builder
│
└── 🤖 agent-meta/                      # Quản lý & Vận hành Agent
    └── using-agent-skills
```

---

### 1. 📐 `architecture-and-specs` (Thiết kế & Đặc tả)
* [`spec-driven-development`](architecture-and-specs/spec-driven-development/SKILL.md) — Phát triển theo hướng đặc tả (SDD): Viết spec rõ ràng trước khi code để tránh làm sai yêu cầu.
* [`documentation-and-adrs`](architecture-and-specs/documentation-and-adrs/SKILL.md) — Ghi nhận quyết định kiến trúc qua Architecture Decision Records (ADRs) và tài liệu kỹ thuật chuẩn.
* [`deprecation-and-migration`](architecture-and-specs/deprecation-and-migration/SKILL.md) — Quy trình migrate thư viện/API cũ sang mới an toàn, có lộ trình và không gây gián đoạn.
* [`context-engineering`](architecture-and-specs/context-engineering/SKILL.md) — Kỹ thuật quản lý và tối ưu ngữ cảnh prompt, tránh quá tải token và loãng thông tin.

### 2. ⚙️ `development-workflows` (Quy trình Lập trình Thực chiến)
* [`test-driven-development`](development-workflows/test-driven-development/SKILL.md) — Quy trình TDD chuẩn mực (Red-Green-Refactor): Viết test trước, code làm pass test sau.
* [`incremental-implementation`](development-workflows/incremental-implementation/SKILL.md) — Triển khai tính năng phức tạp theo từng bước nhỏ (step-by-step), kiểm chứng từng bước.
* [`code-simplification`](development-workflows/code-simplification/SKILL.md) — Tối giản mã nguồn, loại bỏ trừu tượng hóa quá mức (over-abstraction) và dead code.
* [`git-workflow-and-versioning`](development-workflows/git-workflow-and-versioning/SKILL.md) — Quy chuẩn commit thông minh, phân chia branch, xử lý merge conflict và tạo Pull Request.
* [`interview-me`](development-workflows/interview-me/SKILL.md) — Kỹ thuật phỏng vấn ngược người dùng để làm rõ mọi yêu cầu mơ hồ trước khi viết code.
* [`karpathy-guidelines`](development-workflows/karpathy-guidelines/SKILL.md) — 4 nguyên tắc vàng của Andrej Karpathy: Think Before Coding, Simplicity First, Surgical Changes, Goal-Driven Execution.

### 3. 🔍 `debugging-and-observability` (Gỡ lỗi & Giám sát)
* [`debugging-and-error-recovery`](debugging-and-observability/debugging-and-error-recovery/SKILL.md) — Chiến lược cô lập bug, phân tích stack trace và phục hồi hệ thống khi có sự cố.
* [`doubt-driven-development`](debugging-and-observability/doubt-driven-development/SKILL.md) — Lập trình hoài nghi giả định: Luôn kiểm chứng dữ liệu thực tế tại runtime thay vì tin vào giả định.
* [`observability-and-instrumentation`](debugging-and-observability/observability-and-instrumentation/SKILL.md) — Thiết lập logging có cấu trúc, metrics, tracing và health check cho hệ thống.
* [`performance-optimization`](debugging-and-observability/performance-optimization/SKILL.md) — Kỹ thuật profiling, phát hiện bottleneck I/O, CPU, bộ nhớ và tối ưu hóa thời gian phản hồi.

### 4. 🎨 `frontend-and-testing` (Giao diện & Kiểm thử Tự động)
* [`frontend-ui-engineering`](frontend-and-testing/frontend-ui-engineering/SKILL.md) — Kỹ thuật xây dựng UI chất lượng cao, responsive, tối ưu render và accessibility (a11y).
* [`frontend-design`](frontend-and-testing/frontend-design/SKILL.md) — Định hình phong cách thiết kế, typography, bảng màu HSL và micro-interactions cao cấp.
* [`browser-testing-with-devtools`](frontend-and-testing/browser-testing-with-devtools/SKILL.md) — Kiểm thử tự động trên trình duyệt, inspect DOM, chụp console logs và network requests.
* [`webapp-testing`](frontend-and-testing/webapp-testing/SKILL.md) — Bộ công cụ kiểm thử End-to-End tự động cho ứng dụng web bằng Playwright.
* [`web-artifacts-builder`](frontend-and-testing/web-artifacts-builder/SKILL.md) — Xây dựng Artifacts giao diện web đa component (React, Tailwind CSS, shadcn/ui).

### 5. 🤖 `agent-meta` (Vận hành Agent)
* [`using-agent-skills`](agent-meta/using-agent-skills/SKILL.md) — Hướng dẫn và nguyên tắc để AI Agent biết cách tự chọn, đọc và kích hoạt kỹ năng đúng lúc.

---

## 🚀 Hướng Dẫn Sử Dụng (The 3-Tier Delivery Model)

### 📌 Tầng 1: Sử Dụng On-Demand Tức Thì (Tier 1 — Reference)
Dùng cho hầu hết các tác vụ hàng ngày. **Chi phí token: 0 token cố định** (chỉ tải vào cuối ngữ cảnh khi gọi).

```text
# 1. Gọi trực tiếp 1 skill:
"Áp dụng @skills:gh:NguyenKhanhDuy2703/my-skills-agents/development-workflows/test-driven-development để viết unit test cho service này."

# 2. Xem Menu danh mục của một chủ đề:
"@skills:gh:NguyenKhanhDuy2703/my-skills-agents/debugging-and-observability"
```

---

### 💾 Tầng 2: Lưu Vào Dự Án Cụ Thể (Tier 2 — Saved / Vendor)
Dùng khi bạn muốn đưa skill vào quản lý trong Git của dự án để cả team dùng chung hoặc chỉnh sửa theo dự án:

```text
@skills:gh:NguyenKhanhDuy2703/my-skills-agents/development-workflows/karpathy-guidelines:save
```
*Skill sẽ được tải về thư mục `.atskills/` hoặc `.agents/skills/` trong dự án của bạn.*

---

### ⚡ Tầng 3: Tự Động Kích Hoạt (Tier 3 — Auto-Trigger / Install)
Chỉ áp dụng cho **những quy chuẩn bắt buộc** (khuyên dùng $\le 5$ skills). Thêm một dòng vào file `.autotrigger` (hoặc `.atskills/.autotrigger`):

```gitignore
# Tự động kích hoạt nguyên tắc code Karpathy
@gh:NguyenKhanhDuy2703/my-skills-agents/development-workflows/karpathy-guidelines
```

---

### 🗑️ Cách Gỡ Bỏ / Xóa Skill
* **Tắt tự động kích hoạt:** Xóa dòng tương ứng trong file `.autotrigger`.
* **Xóa skill đã lưu nội bộ:** Xóa thư mục skill tương ứng trong `.agents/skills/` hoặc `.atskills/`.

---

## 📄 Giấy phép & Đóng góp
* **Tác giả:** [Nguyen Khanh Duy](https://github.com/NguyenKhanhDuy2703)
* **Quy chuẩn:** Giao thức `@skills` protocol (SylphAI / UT Austin)
* **Giấy phép:** MIT License
