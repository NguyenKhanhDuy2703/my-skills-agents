---
name: reid-osnet-tracking
description: Trích xuất đặc trưng ngoại hình bằng OSNet và so khớp Cosine Similarity để duy trì identity (Re-ID) khi người bị che khuất hoặc rời khỏi khung hình.
---

# Person Re-Identification (Re-ID) with OSNet & Cosine Matching

Skill này hướng dẫn xây dựng bộ nhớ nhận diện ngoại hình (Appearance Feature Memory) kết hợp mô hình OSNet (Omni-Scale Network) nhằm duy trì nhất quán `global_person_id` trên nhiều camera hoặc khi người quay lại khung hình.

---

## 1. Nguyên Lý Hoạt Động

1. **Detection & Crop:** Khi YOLO phát hiện người (`bbox`), trích xuất ảnh crop đối tượng tương ứng.
2. **Feature Extraction:** Chạy ảnh crop qua mạng OSNet (pretrained Re-ID) để thu được vector đặc trưng chuẩn hóa $L_2$ (512 chiều).
3. **Gallery Matching:** So khớp vector mới với Gallery (ngân hàng vector của các ID đã biết) bằng **Cosine Similarity**:
   $$\text{sim}(u, v) = \frac{u \cdot v}{\|u\|_2 \|v\|_2} = u \cdot v \quad (\text{do đã chuẩn hóa } L_2)$$
4. **Decision:** 
   - Nếu $\max(\text{sim}) \ge \text{threshold}$ (thường từ $0.70 \to 0.80$): Gán lại ID cũ và cập nhật (update) vector đặc trưng theo cơ chế Moving Average (EMA).
   - Nếu $< \text{threshold}$: Tạo mới `global_id`.

---

## 2. Code Mẫu Trích Xuất & So Khớp

```python
import numpy as np
import torch
import torchvision.transforms as T
from PIL import Image


class ReIDMatcher:
    """Quản lý Gallery đặc trưng và so khớp Re-ID thời gian thực."""

    def __init__(self, model, similarity_threshold: float = 0.75, ema_alpha: float = 0.9):
        self.model = model.eval()
        self.device = next(model.parameters()).device
        self.similarity_threshold = similarity_threshold
        self.ema_alpha = ema_alpha
        
        # Gallery lưu trữ: { global_id: np.ndarray (512,) }
        self.gallery = {}
        self.next_id = 1

        self.transform = T.Compose([
            T.Resize((256, 128)),
            T.ToTensor(),
            T.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
        ])

    def extract_feature(self, crop_bgr: np.ndarray) -> np.ndarray:
        """Trích xuất feature vector 512-dim đã chuẩn hóa L2."""
        img_rgb = Image.fromarray(crop_bgr[:, :, ::-1])
        tensor = self.transform(img_rgb).unsqueeze(0).to(self.device)

        with torch.no_grad():
            feat = self.model(tensor)
            feat = torch.nn.functional.normalize(feat, p=2, dim=1)
            return feat.cpu().numpy().squeeze(0)

    def match_or_register(self, crop_bgr: np.ndarray) -> tuple[int, float]:
        """So khớp ảnh crop với Gallery hoặc đăng ký ID mới."""
        feat = self.extract_feature(crop_bgr)
        
        best_id = None
        best_score = -1.0

        for gid, gfeat in self.gallery.items():
            # Dot product của 2 vector L2 chính là cosine similarity
            score = float(np.dot(feat, gfeat))
            if score > best_score:
                best_score = score
                best_id = gid

        if best_score >= self.similarity_threshold and best_id is not None:
            # Cập nhật feature theo Exponential Moving Average (EMA)
            updated_feat = self.ema_alpha * self.gallery[best_id] + (1 - self.ema_alpha) * feat
            self.gallery[best_id] = updated_feat / np.linalg.norm(updated_feat)
            return best_id, best_score
        else:
            # Đăng ký ID mới
            new_id = self.next_id
            self.next_id += 1
            self.gallery[new_id] = feat
            return new_id, 1.0
```

---

## 3. Best Practices & Lưu Ý

* **Lọc bỏ ảnh crop kém chất lượng:** Không chạy Re-ID với bounding box có chiều rộng $< 30\text{px}$ hoặc chiều cao $< 60\text{px}$, hoặc bbox chạm mép khung hình bị cắt xén.
* **Thời gian hết hạn Gallery (TTL):** Xóa các ID không xuất hiện sau một khoảng thời gian (ví dụ 30 phút) để tránh Gallery phình to và giảm thiểu va chạm đặc trưng (false positives).
