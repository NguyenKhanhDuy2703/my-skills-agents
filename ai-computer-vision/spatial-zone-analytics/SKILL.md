---
name: spatial-zone-analytics
description: Phân tích hành vi không gian đa giác (Polygon Zones), đếm lượt cắt vạch (Tripwire), tính Dwell Time (thời gian lưu trú) và tích lũy ma trận Heatmap.
---

# Spatial Zone Analytics: Tripwire, Dwell Time & Heatmap

Skill này cung cấp các thuật toán hình học tính toán và phân tích không gian dành cho hệ thống giám sát thông minh (Smart Retail / Surveillance Analytics).

---

## 1. Thuật Toán Kiểm Tra Điểm Trong Đa Giác (Point-in-Polygon)

Để xác định người có nằm trong khu vực quan sát (Zone) hay không, ta lấy điểm chân tiếp đất của người:
$$(x_{\text{foot}}, y_{\text{foot}}) = \left(\frac{x_1 + x_2}{2}, y_2\right)$$

Sử dụng thuật toán Ray-Casting hoặc `cv2.pointPolygonTest`:

```python
import cv2
import numpy as np


def is_point_inside_polygon(point: tuple[float, float], polygon_pts: list[tuple[float, float]]) -> bool:
    """Kiểm tra điểm có nằm trong vùng đa giác tùy ý hay không."""
    poly_array = np.array(polygon_pts, dtype=np.int32)
    # Trả về > 0 nếu nằm trong, 0 nếu nằm trên viền, < 0 nếu nằm ngoài
    res = cv2.pointPolygonTest(poly_array, point, measureDist=False)
    return res >= 0
```

---

## 2. Tính Dwell Time (Thời Gian Lưu Trú) & Zone Crossing

```python
import time


class ZoneTracker:
    def __init__(self, zone_id: str, polygon: list[tuple[float, float]], min_dwell_sec: float = 3.0):
        self.zone_id = zone_id
        self.polygon = polygon
        self.min_dwell_sec = min_dwell_sec
        # Lưu thời điểm bắt đầu vào zone của từng person: { track_id: entry_timestamp }
        self.occupants: dict[int, float] = {}

    def update(self, tracks: list[dict]) -> list[dict]:
        """
        tracks: [{'track_id': int, 'bbox': [x1, y1, x2, y2]}]
        Trả về danh sách sự kiện hoàn thành lưu trú (dwell events).
        """
        current_time = time.time()
        active_ids = set()
        events = []

        for track in tracks:
            tid = track["track_id"]
            x1, y1, x2, y2 = track["bbox"]
            foot_point = ((x1 + x2) / 2.0, y2)

            inside = is_point_inside_polygon(foot_point, self.polygon)
            if inside:
                active_ids.add(tid)
                if tid not in self.occupants:
                    self.occupants[tid] = current_time

        # Kiểm tra những người vừa rời khỏi zone
        exited_ids = set(self.occupants.keys()) - active_ids
        for tid in exited_ids:
            entry_time = self.occupants.pop(tid)
            duration = current_time - entry_time
            if duration >= self.min_dwell_sec:
                events.append({
                    "zone_id": self.zone_id,
                    "track_id": tid,
                    "dwell_time_seconds": round(duration, 2),
                    "exit_time": current_time,
                })

        return events
```

---

## 3. Tích Lũy Ma Trận Heatmap (Density Grid Accumulation)

Thay vì lưu từng điểm tọa độ, ta chia màn hình thành lưới (grid) độ phân giải thấp (ví dụ $64 \times 36$ hoặc $128 \times 72$) và cộng dồn mật độ theo hàm Gaussian:

```python
class HeatmapAccumulator:
    def __init__(self, width: int = 1920, height: int = 1080, grid_scale: int = 16, decay: float = 0.999):
        self.gw = width // grid_scale
        self.gh = height // grid_scale
        self.grid_scale = grid_scale
        self.decay = decay
        self.matrix = np.zeros((self.gh, self.gw), dtype=np.float32)

    def add_points(self, points: list[tuple[float, float]]):
        """Giảm dần mật độ cũ (decay) và cộng dồn điểm mới."""
        self.matrix *= self.decay
        for x, y in points:
            gx = int(x // self.grid_scale)
            gy = int(y // self.grid_scale)
            if 0 <= gx < self.gw and 0 <= gy < self.gh:
                self.matrix[gy, gx] += 1.0

    def get_normalized_heatmap(self) -> np.ndarray:
        """Trả về ma trận [0, 255] dùng cho visualization."""
        if self.matrix.max() > 0:
            norm = (self.matrix / self.matrix.max()) * 255.0
            return norm.astype(np.uint8)
        return np.zeros_like(self.matrix, dtype=np.uint8)
```
