---
name: tailwind-design-system
description: Hướng dẫn xây dựng Design System hiện đại với Tailwind CSS: Semantic tokens, dark mode, glassmorphism, và micro-animations.
---

# Modern Tailwind Design System & UI Architecture

Skill này định nghĩa các nguyên tắc xây dựng giao diện cao cấp, chuyên nghiệp và có chiều sâu (không đơn điệu/generic) với Tailwind CSS.

---

## 1. Nguyên Tắc Thẩm Mỹ (Design Aesthetics)

- **Tránh màu mặc định đơn điệu:** Không dùng các màu thuần `bg-red-500`, `bg-blue-500`. Thay vào đó, sử dụng bảng màu HSL tinh tế hoặc các tone Slate/Zinc/Neutral có chiều sâu.
- **Glassmorphism & Depth:** Sử dụng `backdrop-blur-md bg-neutral-900/80 border border-white/10` kết hợp `shadow-2xl` để tạo cảm giác sang trọng.
- **Typography Hiện Đại:** Sử dụng các font sans-serif chất lượng cao như `Inter`, `Plus Jakarta Sans` hoặc `Outfit`.
- **Micro-animations:** Thêm hiệu ứng chuyển động mượt mà khi hover (`transition-all duration-200 hover:scale-[1.02] active:scale-[0.98]`).

---

## 2. Bảng Tokens Mẫu Trong `tailwind.config.js`

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ["class"],
  content: ["./src/**/*.{ts,tsx,html}"],
  theme: {
    extend: {
      colors: {
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
    },
  },
  plugins: [],
};
```

---

## 3. CSS Variables Toàn Cục (`index.css`)

```css
@layer base {
  :root {
    --background: 224 71% 4%;
    --foreground: 213 31% 91%;
    --card: 224 71% 7%;
    --card-foreground: 213 31% 91%;
    --primary: 210 100% 52%;
    --primary-foreground: 0 0% 100%;
    --accent: 216 34% 17%;
    --accent-foreground: 210 40% 98%;
    --radius: 0.75rem;
  }
}
```
