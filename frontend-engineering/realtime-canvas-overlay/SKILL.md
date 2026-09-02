---
name: realtime-canvas-overlay
description: Vẽ Bounding Box, Tracking Trail và Heatmap thời gian thực lên HTML5 Canvas đồng bộ với luồng Video streaming.
---

# Realtime Video Canvas Overlay

Skill này cung cấp kỹ thuật vẽ các thành phần trực quan AI (Bounding Boxes, Center Trail, Zone Polygons, Dwell Badge) chồng lên khung hình Video bằng HTML5 2D Canvas với hiệu năng cao (60 FPS).

---

## 1. Nguyên Tắc Đồng Bộ Tọa Độ (Coordinate Scaling)

Tọa độ AI trả về thường nằm trên độ phân giải gốc của camera (ví dụ $1920 \times 1080$), trong khi thẻ `<video>` hoặc `<canvas>` hiển thị theo kích thước CSS responsive. 

**Công thức chuẩn hóa:**
$$\text{scaleX} = \frac{\text{canvas.width}}{\text{videoOriginalWidth}}, \quad \text{scaleY} = \frac{\text{canvas.height}}{\text{videoOriginalHeight}}$$

---

## 2. Component Vẽ Canvas Chuẩn (TypeScript / React)

```typescript
export interface DetectionBox {
  trackId: number;
  bbox: [number, number, number, number]; // [x1, y1, x2, y2]
  confidence: number;
  label?: string;
}

export function drawDetections(
  ctx: CanvasRenderingContext2D,
  detections: DetectionBox[],
  canvasWidth: number,
  canvasHeight: number,
  srcWidth: number = 1920,
  srcHeight: number = 1080
) {
  // 1. Xóa sạch canvas trước mỗi frame
  ctx.clearRect(0, 0, canvasWidth, canvasHeight);

  const scaleX = canvasWidth / srcWidth;
  const scaleY = canvasHeight / srcHeight;

  ctx.lineWidth = 2;
  ctx.font = "12px Inter, sans-serif";

  detections.forEach((det) => {
    const [x1, y1, x2, y2] = det.bbox;
    const rx = x1 * scaleX;
    const ry = y1 * scaleY;
    const rw = (x2 - x1) * scaleX;
    const rh = (y2 - y1) * scaleY;

    // Màu sắc theo track_id
    const hue = (det.trackId * 137.5) % 360;
    const boxColor = `hsl(${hue}, 80%, 55%)`;

    // Vẽ Bounding Box bo góc nhẹ
    ctx.strokeStyle = boxColor;
    ctx.strokeRect(rx, ry, rw, rh);

    // Vẽ nhãn ID
    const label = `ID #${det.trackId} (${Math.round(det.confidence * 100)}%)`;
    const textWidth = ctx.measureText(label).width;

    ctx.fillStyle = boxColor;
    ctx.fillRect(rx, ry - 18, textWidth + 8, 18);

    ctx.fillStyle = "#ffffff";
    ctx.fillText(label, rx + 4, ry - 4);
  });
}
```

---

## 3. Quản Lý Vòng Lặp Vẽ (Render Loop)

* Sử dụng `requestAnimationFrame` kết hợp với buffer dữ liệu nhận từ WebSocket.
* Không gọi trực tiếp `draw()` trong callback của WebSocket event để tránh giật lag khi có bùng nổ dữ liệu (message burst).
