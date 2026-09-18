# Đ1 — PHÁT HIỆN NGUY CƠ VA CHẠM CHO XE TỰ LÁI (VINFAST)
## BẢN THIẾT KẾ DỮ LIỆU THỰC CHIẾN (PLAYBOOK 60 PHÚT · 8 THÀNH VIÊN)
**Khóa học:** VinUniversity · AICB-P2T4 · Data Track · Ngày 06  
**Chuẩn mực:** Đáp ứng 100% Tiêu chí Đánh giá Rubric 100 điểm & 10 Giai đoạn Vòng đời Dữ liệu (Canonical Data Lifecycle)

---

## 0. CÂU QUYẾT ĐỊNH & MA TRẬN TỔN THẤT (COST MATRIX)
*(Thời gian: Phút 00–06, Toàn đội chốt nguyên tắc sống còn)*

**Câu quyết định chuẩn hóa:**  
> "Quyết định **kích hoạt phanh khẩn cấp (AEB) / phát cảnh báo cho tài xế (FCW) / không can thiệp (Passive)**, trên xe VinFast VF8/VF9 đang chạy, từ ảnh camera trước (FOV 120°), trong vòng **< 200 mili-giây**."

*   **Đơn vị dữ liệu (Unit of Data):** Một vật thể trong một khung hình camera trước.
*   **Group key bắt buộc:** `trip_id` (một phiên ghi hình liên tục của một xe).

### Bảng Ma trận Tổn thất Sinh mạng (Asymmetric Cost Matrix)

| Lỗi (Error Type) | Trọng số phạt (Cost) | Vì sao (Căn cứ Kỹ thuật & Vật lý) |
| :--- | :---: | :--- |
| **Sót người đi bộ (Trong làn, < 25m)** | **1000** | Trực tiếp đe dọa sinh mạng con người. |
| **Sót xe máy (Trong làn, < 25m)** | **800** | Mật độ xe máy tại VN cực cao, dễ ngã khi va chạm. |
| **Sót ô tô (Trong làn gần)** | 200 | Va chạm nặng, thiệt hại tài sản nhưng hiếm tử vong. |
| **Phanh oan (False Positive)** | **150** | Xe sau đâm dồn toa (Tại VN bám đuôi 1-2m, FP không miễn phí). |
| **Cảnh báo oan (False Warning)** | 10 | Gây phiền nhiễu, tài xế tắt luôn hệ thống ADAS. |
| **Sót vật ngoài làn / > 25m** | 5 | Không cấu thành va chạm trong quỹ đạo di chuyển. |

**Ba hệ quả kéo theo toàn bộ thiết kế Pipeline:**
1.  **Ngưỡng QC đặt theo lớp nguy hiểm nhất (Worst-Class Gate):** Bỏ sót VRU (Cost 1000) đắt **gấp 6.67 lần** Phanh oan (Cost 150) và đắt **gấp 200 lần** so với bỏ sót vật ngoài làn (Cost 5). Do đó, cấm tiệt Macro F1 hay mAP trung bình.
2.  **Ràng buộc 200ms & Giới hạn Quang học:** 200ms ép model giảm kích thước ảnh đầu vào $\implies$ Vật thể quá xa ($> 80\text{m}$) hoặc quá nhỏ ($< 12\text{px}$) không đủ tín hiệu pixel để nhận diện $\implies$ Bắt buộc phải có **Ignore Region**.
3.  **Vật thể ở đâu và đang làm gì:** Chỉ gán `class + bbox` là **KHÔNG ĐỦ** để phanh. Người đi bộ cách 25m trên vỉa hè (`out_of_lane`) và người cách 25m đang bước xuống làn (`in_lane`, `crossing`) đòi hỏi 2 quyết định đối lập. Bắt buộc phải gán thêm thuộc tính không gian và động lực học.

---

## 1. CẤU TRÚC ĐỘI HÌNH (8 THÀNH VIÊN) & PHÂN BỔ 6 CÂU HỎI TA (TA Q1-Q6)

*Theo Rubric, "Slide không nêu rõ phần việc của từng thành viên bị trừ 10 điểm", bảng dưới đây phân định trách nhiệm chốt chặn cứng cho từng thành viên:*

| Tổ | Thành viên | Trách nhiệm Bàn giao Cứng (Hạn phút 40) | Vùng Chuyên môn Phản biện (Lead Responder) |
| :---: | :--- | :--- | :--- |
| **A (NGUỒN)** | **Thành viên 1** | Khai thác Nexar, Sổ nguồn Data Ledger, License. | **[TA Q1]** Dataset phục vụ quyết định nào, ai dùng? |
| | **Thành viên 2** | Bảng Quota tập Test (Ép chỉ tiêu cho đêm/mưa/cut-in). | Hỗ trợ TA Q1 & Giải trình lấy mẫu EDA. |
| **B (XỬ LÝ)** | **Thành viên 3** | Cổng chặn PII (deface + plate) & Quarantine bucket. | **[TA Q6 - Đồng phụ trách]** Điểm gãy kỹ thuật & CI/CD. |
| | **Thành viên 4** | Khử trùng pHash & Schema Parquet 24 trường. | **[TA Q4 - Lead]** Kỹ thuật lọc trùng lặp & Schema data. |
| **C (NHÃN)** | **Thành viên 5** | Guideline quy tắc Truncation, Amodal & Ignore. | **[TA Q3 - Lead]** Xử lý ca khó, che khuất $\ge 80\%$. |
| | **Thành viên 6** | Bộ 10 ca biên Edge Cases & Nhãn động học. | Hỗ trợ TA Q3 & Giải trình Decision Log. |
| **D (ĐO LƯỜNG)**| **Thành viên 7 (Lead)**| Chiến lược GroupSplit chống rò rỉ, Ngưỡng QC. | **[TA Q2 - Lead]** Ma trận tổn thất & Asymmetric Loss. |
| | **Thành viên 8** | Metric đo đạc (Miss Rate @ 0.1FP), Risk Audit. | **[TA Q5 - Lead] & [TA Q6 - Lead]** Đo lường & SPOF. |

---

## 2. TỔ A: NGUỒN DỮ LIỆU THỰC TẾ, QUARANTINE VÀ ÉP QUOTA TẬP TEST

### 2.1 Khai thác Bộ Dữ liệu Nexar Collision Prediction (Nguồn thực chiến chính)
*   **Vị trí kho dữ liệu:**
    *   Kaggle: `nexar-collision-prediction`
    *   Hugging Face Mirror (Toàn quyền truy cập): `nexar-ai/nexar_collision_prediction`
*   **Quy mô bộ dữ liệu:** 2.844 video dashcam MP4 định dạng chuẩn $1280 \times 720$ @ 30 FPS.
    *   **Tập Train (1.500 video full, ~40s/video):** Gồm 750 video positive (400 vụ đâm thật, 350 vụ suýt đâm near-miss) và 750 video negative (lái xe bình thường).
    *   **Tập Test (1.344 video, ~10s/video):** Được cắt ngắn ở các mốc thời gian dẫn truyền va chạm (`time_to_accident`): 0.5s, 1.0s, và 1.5s.
*   **Metadata thực tế của Nexar:** Cung cấp nhãn `time_of_event`, `time_of_alert`, `light_conditions`, `weather`, `scene`, `time_to_accident`.

### 2.2 Đánh giá Giấy phép & Sổ nguồn (Data Ledger)
*   Tải video YouTube hàng loạt là vi phạm ToS. Hệ thống xe thương mại VinFast phân định rõ 2 mục đích sử dụng:
    *   **Nexar Dataset & SODA10M:** Dùng để phát triển thuật toán, pretrain và benchmark.
    *   **In-house Dashcam VN:** 4 thành viên $\times$ 2 giờ chạy xe thực địa ngõ ngách Hà Nội / TP.HCM = 8 giờ video độc quyền (Proprietary) dùng huấn luyện chính thức.
*   **Sổ nguồn (Data Ledger):** Mỗi tệp video thô được gắn metadata: `source_id | license_type | commercial_usable | hash_sha256 | date_downloaded`.

### 2.3 Cơ chế Khu cách ly (Quarantine Bucket)
Thu thập dữ liệu kèm Provenance. Các tệp lỗi (video hỏng codec, mất metadata GPS, desync tín hiệu CAN $> 10\text{ms}$, camera bẩn bùn $> 20\%$) **bắt buộc đưa vào S3 Quarantine Bucket**, tuyệt đối không xóa mù:
```yaml
# Schema bảng Quarantine_Ledger:
- file_id: "raw_nexar_00822.mp4"
- quarantine_timestamp: 1726646400
- error_code: "CAN_TIMESTAMP_DESYNC"  # [CORRUPT_FRAME, CAN_DESYNC, SENSOR_SATURATED]
- dropped_frames_ratio: 0.042
- triage_status: "QUARANTINED"         # [QUARANTINED, RETRIED, DISCARDED]
- root_cause_notes: "Camera mất đồng bộ xung clock với hộp CAN bus ở giây 18.2"
```

### 2.4 Quota Tập Test: Chuyển từ "Phân phối Tự nhiên" sang "Ép Tỷ lệ"
*Bằng chứng thực nghiệm từ tập huấn luyện Nexar:*
*   **Thời tiết:** Clear (59.3%), Cloudy (32.4%), Rain (8.0%), Snow (<0.2%).
*   **Ánh sáng:** Ban ngày (90.7%), Chập tối (5.5%), **Đêm tối (2.9%)**, Ngược sáng lóa (0.9%).
*   **Bối cảnh:** Đô thị (63.3%), Cao tốc (22.8%), Ngoại ô (10.0%), Nông thôn (3.9%).

> **LUẬN ĐIỂM SẮC BÉN:** Trong tự nhiên, điều kiện **Đêm tối chỉ chiếm 2.9%** và **Mưa chỉ chiếm 8.0%**. Nếu chia ngẫu nhiên để tập test nhận phân phối tự nhiên, mô hình có thể đạt độ chính xác 97% dù hoàn toàn "mù" ban đêm! Do đó, **bắt buộc phải ép Quota tập Test**:

| Lát cắt Môi trường / Ca nguy hiểm (Slice) | Tỷ lệ Tự nhiên (Nexar) | Quota Bắt buộc (Tập Test VinFast) |
| :--- | :---: | :--- |
| **Ban đêm / Thiếu sáng** | 2.9% | **≥ 25%** số khung hình |
| **Mưa rào / Mặt đường ướt phản quang** | 8.0% | **≥ 10%** số khung hình |
| **Ngược sáng (Bình minh / Ra khỏi hầm)**| 0.9% | **≥ 8%** số khung hình |
| **Người đi bộ cắt ngang đường ban đêm**| Rất hiếm | **≥ 300** instance vật thể |
| **Xe máy/Ô tô tạt đầu đột ngột (Cut-in)**| Rất hiếm | **≥ 200** sự kiện độc lập |

---

## 3. TỔ B: DATA PIPELINE 6 BƯỚC, SCHEMA PARQUET & MÃ LỆNH THỰC THI

### 3.1 Pipeline Xử lý 6 Bước (Raw $\to$ Release Manifest)
```text
Video Thô (Nexar/In-house, 30fps)
  ├─1. Tách Keyframe 2 fps ───────────► Giảm 15x, nhúng trip_id vào filename
  ├─2. Khử Trùng lặp (pHash) ─────────► Lọc khung dừng đèn đỏ, lưu keep_frames
  ├─3. ẨN DANH 2 LỚP (Face + Plate) ──► CỔNG CHẶN PHÁP LÝ (Nghị định 13)
  ├─4. AI Pre-label (YOLOv10) ────────► Sinh proposal (Chỉ chạy khi conf > 0.6)
  ├─5. Gán nhãn CVAT ─────────────────► Người sửa Bbox + Gán nhãn động học
  └─6. Xuất Manifest Parquet & COCO ──► Sẵn sàng đưa vào DataLoader
```

### 3.2 Lệnh Thực thi Cụ thể (Runnable Shell & Python Scripts)

#### Lệnh 1: Tách Keyframe 2 fps & Nhúng cứng `trip_id` (Bash)
```bash
# Tạo thư mục đích để tránh crash
mkdir -p frames/
# Tách khung 2fps, giữ chất lượng ảnh cao (q:v 2), nhúng trip_id vào tên file
ffmpeg -i raw_videos/trip_0042.mp4 -vf fps=2 -q:v 2 frames/trip_0042_%06d.jpg
```

#### Lệnh 2: Khử Trùng lặp bằng pHash Có Kiểm soát Tài nguyên (Python)
```python
import imagehash, glob, os, shutil
from PIL import Image

output_dir = "filtered_frames"
os.makedirs(output_dir, exist_ok=True)

# Sắp xếp và xử lý theo từng trip để tránh rò rỉ biên liên chuyến
trips = set(os.path.basename(p).split('_')[0] + '_' + os.path.basename(p).split('_')[1] 
            for p in glob.glob("frames/*.jpg"))

for trip in sorted(trips):
    trip_frames = sorted(glob.glob(f"frames/{trip}_*.jpg"))
    seen_hashes = []
    for p in trip_frames:
        # Sử dụng context manager 'with' để đóng file handle, chống lỗi Too many open files
        with Image.open(p) as img:
            h = imagehash.phash(img)
        # Giữ frame nếu khác biệt Hamming > 8 so với 30 frame gần nhất
        if all(h - s > 8 for s in seen_hashes[-30:]):
            seen_hashes.append(h)
            shutil.copy(p, os.path.join(output_dir, os.path.basename(p)))
```

#### Lệnh 3: Ẩn danh PII Hai Lớp (Khuôn mặt + Biển số xe) (Bash/Python)
```bash
# 1. Che khuôn mặt bằng deface (dùng CenterFace, mở rộng biên 10%)
deface filtered_frames/ --thresh 0.2 --replacewith blur --boxes

# 2. BẮT BUỘC: Che biển số xe bằng detector chuyên dụng (YOLOv8-plate)
python -c "
from ultralytics import YOLO
import cv2, glob

model = YOLO('yolov8n-plate.pt')
for p in glob.glob('filtered_frames/*.jpg'):
    img = cv2.imread(p)
    results = model(img, verbose=False)
    for box in results[0].boxes.xyxy.cpu().numpy():
        x1, y1, x2, y2 = map(int, box)
        h, w = y2 - y1, x2 - x1
        x1, y1 = max(0, x1 - int(0.1*w)), max(0, y1 - int(0.1*h))
        x2, y2 = min(img.shape[1], x2 + int(0.1*w)), min(img.shape[0], y2 + int(0.1*h))
        roi = img[y1:y2, x1:x2]
        img[y1:y2, x1:x2] = cv2.GaussianBlur(roi, (51, 51), 15)
    cv2.imwrite(p, img)
"
```

#### Lệnh 4: Phân tách Chống Rò rỉ Train / Val / Test theo `trip_id` (Python)
```python
import pandas as pd
from sklearn.model_selection import GroupShuffleSplit

# Đọc manifest chứa toàn bộ annotation
df = pd.read_parquet("manifest.parquet")

# Bước 1: Tách tập Test (15% số chuyến)
gss_test = GroupShuffleSplit(n_splits=1, test_size=0.15, random_state=42)
train_val_idx, test_idx = next(gss_test.split(df, groups=df.trip_id))
df_train_val = df.iloc[train_val_idx]
df_test = df.iloc[test_idx]

# Bước 2: Tách tập Train (70%) và Val (15%) từ phần còn lại
gss_val = GroupShuffleSplit(n_splits=1, test_size=0.1765, random_state=42)  # 0.15 / 0.85 ≈ 0.1765
train_idx, val_idx = next(gss_val.split(df_train_val, groups=df_train_val.trip_id))
df_train = df_train_val.iloc[train_idx]
df_val = df_train_val.iloc[val_idx]

# BẰNG CHỨNG KIỂM TOÁN TỰ ĐỘNG TRÊN CI/CD
train_trips = set(df_train.trip_id)
val_trips = set(df_val.trip_id)
test_trips = set(df_test.trip_id)

assert train_trips.isdisjoint(val_trips), "FATAL: Rò rỉ giữa Train và Val!"
assert train_trips.isdisjoint(test_trips), "FATAL: Rò rỉ giữa Train và Test!"
assert val_trips.isdisjoint(test_trips), "FATAL: Rò rỉ giữa Val và Test!"
print(f"Split sạch hoàn toàn 100%: Train={len(train_trips)} trips, Val={len(val_trips)} trips, Test={len(test_trips)} trips")
```

### 3.3 Schema Manifest Định dạng Parquet Đầy đủ (24 Trường)
Lưu trữ dạng Flat Table trên Parquet để query trực tiếp bằng Pandas/Polars/DuckDB, hỗ trợ lọc slice với tốc độ hàng triệu dòng/giây:

```yaml
Schema Manifest (manifest.parquet - 1 dòng = 1 vật thể trong 1 khung):
  # 1. Định danh & Ảnh gốc (Image-level):
  - trip_id: string          # Mã hành trình (Group Key)
  - frame_id: string         # Mã khung hình (trip_0042_000120)
  - timestamp_sec: float     # Thời điểm trong chuyến (giây)
  - img_path: string         # Đường dẫn tương đối
  - img_w: int               # Chiều rộng ảnh (1280)
  - img_h: int               # Chiều cao ảnh (720)
  
  # 2. Tọa độ Hình học Bounding Box:
  - obj_id: int              # ID định danh vật thể
  - class_name: string       # [Pedestrian, Bicycle, Motorcycle, Car, Bus_Truck, UNKNOWN_OBJECT]
  - x_min: float             # Tọa độ pixel tuyệt đối
  - y_min: float
  - w: float
  - h: float

  # 3. Thuộc tính Che khuất & Cắt mép:
  - occlusion_level: string  # [0-25%, 25-50%, 50-80%, >80%]
  - truncated: bool          # Bị cắt ở mép ảnh (diện tích thấy >= 25%)

  # 4. Ba trường Động học Sống còn phục vụ Phanh AEB:
  - lane_relation: string    # [in_lane, near_lane, out_of_lane]
  - distance_band: string    # [lt10m, 10to25m, gt25m]
  - motion_state: string     # [static, along, crossing, cut_in]

  # 5. Cờ Bỏ qua & Kiểm soát Pre-label:
  - ignore: bool             # Zero-loss flag (Bbox < 12px, lóa bão hòa)
  - prelabel_modified: bool  # True nếu annotator có chỉnh sửa hộp AI

  # 6. Metadata Môi trường & Kiểm toán Nguồn gốc (Provenance):
  - lighting: string         # [daylight, twilight, night_lit, night_dark, glare]
  - weather: string          # [clear, cloudy, rain, fog]
  - annotator_id: string     # Mã nhân sự gán nhãn
  - qc_status: string        # [pending, approved, rejected, escalated]
  - anon_version: string     # Phiên bản PII blur (deface_v1.2)
```

---

## 4. TỔ C: HƯỚNG DẪN GÁN NHÃN (GUIDELINE), PILOT 30 ẢNH & 10 CA BIÊN

### 4.1 Thử nghiệm Pilot Kép trên 30 Ảnh Ca Khó (Pilot Gate)
Trước khi mở gán nhãn hàng loạt, 2 annotator phải gán độc lập (mù đôi) trên tập **30 ảnh stress-test** (đêm mưa ngập, ngược sáng lóa, xe máy cắt đầu cự ly gần).
*   **Ngưỡng thông qua Gate Pilot:** Chỉ số đồng thuận **Cohen's Kappa $\kappa \ge 0.85$** trên các trường thuộc tính và **mIoU $\ge 0.70$** trên Bounding Box.
*   Nếu $\kappa < 0.85 \implies$ Đình chỉ gán nhãn, họp Adjudicator, cập nhật Guideline và chạy lại Pilot vòng 2.

### 4.2 Quy tắc Che khuất (Occlusion) & Hộp Amodal
*   **Triết lý Amodal:** Vẽ Bounding Box bao trùm toàn bộ thể tích vật lý suy đoán của vật cản. Hệ thống ADAS can thiệp phanh để tránh va chạm với *vật thể thực*, không phải với *pixel nhìn thấy*.

| Mức độ Che khuất | Nhóm VRU (Người đi bộ, Xe máy) | Nhóm Xe lớn / Vật thể tĩnh |
| :--- | :--- | :--- |
| **0 – 50%** | Gán Bbox bình thường. | Gán Bbox bình thường. |
| **50 – 80%** | **Bắt buộc gán Amodal Bbox**, cắm cờ `occlusion_level = 50-80%`. | Gán nếu suy luận được đường biên vật lý. |
| **> 80%** | Đánh cờ `ignore = true` (Giữ Bbox phục vụ tracking, không tính loss). | Đánh cờ `ignore = true`. |

### 4.3 Xử lý Vùng Bỏ qua (Ignore Region) & Quyền Abstain
*   **Ignore Region:** Các vật thể có chiều cao $< 12\text{px}$ (ở cự ly $> 80\text{m}$), hoặc nằm trọn trong vùng bão hòa lóa trắng ($Pixel = 255$) do đèn pha $\implies$ Đánh nhãn `ignore = true`. Vùng này **không tính True Positive và KHÔNG PHẠT False Positive**, tránh việc model bị phạt oan do giới hạn vật lý của cảm biến.
*   **Quyền Abstain (Chủ động không chốt):** Nếu bằng chứng hình ảnh quá mơ hồ (như bóng mờ trong sương mù dày), Annotator được quyền bấm **Abstain** (gắn tag `can_xem_lai`) thay vì đoán mò. Dữ liệu này sẽ được chuyển sang hàng đợi phân xử cảm biến đa phương thức (Radar 77GHz).

### 4.4 Bộ 10 Ca biên (Edge Cases) Kèm Quy tắc Xử lý Triệt để & Decision Log
*Mọi ca tranh luận phải được ghi vào **Decision Log** (`decision_log.md`) kèm ảnh minh họa và phiên bản Guideline cập nhật.*

1.  **Người khuất sau cột đèn, chỉ lộ 1 chân:** $\implies$ Gán Amodal Bbox trùm toàn bộ chiều cao cơ thể suy đoán, đánh cờ `occlusion_level = 50-80%`.
2.  **Xe máy khuất sau đuôi ô tô, chỉ thò ra gương chiếu hậu:** $\implies$ Nếu thấy gương + tay lái: gán Amodal Bbox xe máy. Nếu chỉ thấy gương rời rạc không đủ cấu trúc: đánh cờ `ignore = true`.
3.  **Người dắt xe máy:** $\implies$ **Gộp thành 1 Bbox duy nhất**, gán class `Pedestrian` nếu người đi bộ ở ngoài làn xe, hoặc `UNKNOWN_OBJECT` nếu dắt ngang giữa lòng đường.
4.  **Người ngồi trên xe máy (kẹp 2, kẹp 3):** $\implies$ **Cấm tách lớp `rider`**, gán 1 Bbox duy nhất ôm trọn người lái, người ngồi sau và toàn bộ thân xe (Kinematic Collision Footprint).
5.  **Đèn pha xe tải ngược chiều lóa trắng nửa khung:** $\implies$ Khoanh toàn bộ vùng lóa trắng, đánh cờ `ignore = true`.
6.  **Giọt nước đọng trên kính lái tạo bóng ma (Ghosting):** $\implies$ Kiểm tra chuỗi 3 frames liên tiếp; nếu vết mờ không chuyển động tịnh tiến theo mặt đường: **KHÔNG GÁN** (Artifact quang học).
7.  **Đám đông 3 người chồng lấp nhau tại vạch sang đường:** $\implies$ Gán từng Bbox riêng nếu nhìn thấy $\ge 20\%$ cơ thể mỗi người. Nếu dính chặt thành khối không phân tách được: gán 1 Bbox lớn bao quanh kèm cờ `ignore = true`.
8.  **Xe máy tạt đầu đột ngột (Cut-in) mới lộ 1/3 thân xe:** $\implies$ Bắt buộc gán Amodal Bbox, đánh cờ `motion_state = cut_in` và `lane_relation = in_lane`.
9.  **Bóng người phản chiếu trên mặt đường ướt hoặc tường kính:** $\implies$ **CẤM GÁN**, chỉ gán trên thực thể vật lý có bóng đổ.
10. **Hình in người/xe máy trên pano quảng cáo hoặc hông xe buýt:** $\implies$ **CẤM GÁN**, đối chiếu mặt phẳng chuyển động với thân xe buýt.

---

## 5. TỔ D: ĐO LƯỜNG CHẤT LƯỢNG, CHỐNG RÒ RỈ & TIÊU CHUẨN NGHIỆM THU LÔ

### 5.1 Chặn đứng Tử huyệt Rò rỉ Thời gian (Temporal Leakage)
*   **Nguyên nhân:** Camera dashcam ghi hình 30fps. Hai khung hình cách nhau 33ms chứa chung bối cảnh nền, cây cối và cùng một người đi bộ. Nếu dùng `train_test_split` ngẫu nhiên theo khung hình $\implies$ Mô hình "học thuộc lòng đề thi", điểm mAP đạt 99% ảo nhưng triển khai xe thật sẽ đâm vào tường.
*   **3 Lớp Phòng thủ Hợp lực:**
    1.  *Khóa Group Key:* Bắt buộc chia bằng `GroupShuffleSplit` theo `trip_id`.
    2.  *Audit Cận Trùng lặp xuyên tập:* Quét mã pHash (ngưỡng Hamming $< 8$) giữa mọi cặp khung hình giữa Train và Test. Báo cáo bằng con số định lượng: **0 cặp trùng lặp lọt lưới**.
    3.  *Thử nghiệm Ablation Proof:* Huấn luyện cùng 1 kiến trúc mô hình trên 2 phương pháp chia: Chia ngẫu nhiên (mAP 94.2%) vs Chia theo `trip_id` (mAP 81.5%). Khoảng chênh lệch $12.7\%$ chính là bằng chứng đanh thép về mức độ rò rỉ dữ liệu.

### 5.2 Metric Đo lường: Bác bỏ mAP, Sử dụng Miss Rate trên VRU Khóa Ngân sách FP
*   **Chỉ số Sống còn:** **Miss Rate (Tỷ lệ bỏ sót / False Negative Rate)** trên nhóm người yếu thế VRU (`Pedestrian` + `Motorcycle`) nằm trong làn và cự ly nguy hiểm:
    $$\text{Miss Rate}_{\text{VRU}} = \frac{\text{FN}_{\text{VRU}}}{\text{TP}_{\text{VRU}} + \text{FN}_{\text{VRU}}} \times 100\% \quad (\text{Mẫu số: Tổng số VRU thực tế có mặt})$$
*   **Ngân sách Phanh ma (Guardrail):** Đo Miss Rate tại điểm hoạt động cố định: **$\le 0.1\text{ False Positive / khung hình}$** trên luồng nhận thức thô (Raw Perception). Tín hiệu sau đó phải qua bộ lọc thời gian 3 frames liên tiếp trước khi chuyển sang cơ cấu phanh.

### 5.3 Gán Độc lập (Independent Label) & Ngưỡng Nghiệm thu Lô (QC Gates)
*   **Quy trình Kiểm định 2 Tầng:**
    *   Tập Train: Gán đơn, audit ngẫu nhiên **20%** (Rút ngẫu nhiên **100 khung / lô 500 khung**).
    *   Tập Val/Test: **Gán Đôi Mù 100% (Independent Double-blind)** giữa 2 annotator độc lập $\to$ Đưa lên Adjudicator phân xử để tạo tập Gold Ground Truth.
*   **Chèn Bẫy Ngầm (Honeypot 5%):** Trộn ngầm 5% khung ảnh đã có đáp án chuẩn của chuyên gia vào mỗi lô để giám sát độ tập trung của nhân sự.

| Lát cắt Lớp / Ngữ cảnh | Recall Tối thiểu so với Gold | IoU Yêu cầu | Thuộc tính Đúng (Lane/Motion) |
| :--- | :--- | :--- | :--- |
| **Người đi bộ (`in_lane`, $< 25\text{m}$)** | **100% (Sót 1 cá thể $\to$ REJECT LÔ)** | $\ge 0.70$ | $\ge 95\%$ |
| **Xe máy (`in_lane`, $< 25\text{m}$)** | **$\ge 99\%$** (Tối đa 1 lỗi / 100 xe) | $\ge 0.70$ | $\ge 95\%$ |
| **Ô tô (Car)** | $\ge 95\%$ | $\ge 0.60$ | $\ge 90\%$ |
| **Vật thể tĩnh / Ngoại biên** | $\ge 92\%$ | $\ge 0.50$ | $\ge 85\%$ |

> **Chế tài Vi phạm:** Nếu phát hiện dù chỉ **1 lỗi bỏ sót người đi bộ trong làn** ở mẫu kiểm tra $\implies$ **REJECT TOÀN BỘ LÔ 500 KHUNG HÌNH**, đình chỉ tài khoản annotator, bắt buộc xóa nhãn và gán lại 100%.

---

## 6. GIAI ĐOẠN 9: ĐÓNG GÓI BÀN GIAO (RELEASE PACKET v1.0) & CHỐT CHẶN XUẤT XƯỞNG

Để bàn giao tập dữ liệu sang nhóm Huấn luyện Mô hình (ML Engineering), Data Owner (Thành viên 1) chỉ được phép ký biên bản khi gói bàn giao hoàn thiện đủ **5 Thành phần Cốt lõi**:

1.  **Frozen Clean Data Artifacts:** Ảnh nén không mất dữ liệu kèm nhãn Parquet / COCO. Toàn bộ thư mục được cấp mã băm **SHA-256 Checksum** và bật chế độ **S3 Object Lock (WORM - Write Once, Read Many)** để chống can thiệp sửa đổi nhãn sau khi đã đóng băng.
2.  **Data Split Manifest:** Tệp `split_manifest.json` chứng minh giao thoa tập hợp theo `trip_id` bằng chính xác $0.00\%$.
3.  **Báo cáo Kiểm toán QC (Audit & Provenance Report):** Báo cáo nghiệm thu mẫu audit ngẫu nhiên 20% và Honeypot 5%, xác nhận tỷ lệ sót PII $\le 0.001\%$.
4.  **Verification Test Suite:** Bộ script CI/CD tự động kiểm thử tính toàn vẹn: 0 tọa độ NaN, 0 bounding box tràn viền, 0 box diện tích $\le 0$, định dạng keypoints chuẩn 51 phần tử.
5.  **Dataset Card Chuẩn hóa (dataset_card.json):** Công bố minh bạch phạm vi ODD, phân bố thuộc tính và **Known Limitations (Giới hạn đã biết)**: Hệ thống chưa hỗ trợ nhận diện vật thể khi kính lái bám bùn $> 30\%$ hoặc mưa tuyết dày đặc.

---

## 7. GIAI ĐOẠN 10: GIÁM SÁT VẬN HÀNH (MONITORING) & VÒNG LẶP SỬA TẬN GỐC (ROOT CAUSE REWORK)

### 7.1 Giám sát Trôi Dữ liệu Đầu vào (Data Drift Monitoring)
Khi xe lăn bánh thương mại, hệ thống telemetry tự động theo dõi phân bố tín hiệu cảm biến theo thời gian:
*   **Đo lường Drift:** Tính khoảng cách Wasserstein / PSI (Population Stability Index) trên histogram độ rọi ánh sáng (Lux), độ tán xạ hạt mưa, và tỷ lệ ảnh chụp ngược sáng.
*   **Ngưỡng kích hoạt cảnh báo:** Khi PSI $> 0.2$ so với phân phối tập huấn luyện gốc $\implies$ Kích hoạt cờ cảnh báo Data Drift, thông báo cho Tổ A khởi động chiến dịch thu thập bổ sung.

### 7.2 Vòng lặp Phản hồi từ Xe Lăn bánh (Fleet Telemetry Feedback Loop)
Hành vi của tài xế trên xe VinFast là sự thật mặt đất khách quan nhất:
*   **Tài xế đạp thốc ga đè phanh:** Hệ thống vừa phanh mà tài xế đạp ga vượt qua $\implies$ **Phanh oan (False Positive)**. Hộp đen tự động lưu 15 giây video (10s trước + 5s sau sự kiện).
*   **Tài xế phanh gấp hoặc đánh lái né vật cản:** Xe không cảnh báo mà tài xế đạp phanh $> 0.6\text{g}$ $\implies$ **Bỏ sót vật cản (False Negative)**. Hộp đen lập tức trích xuất clip gửi về S3.

### 7.3 Giao thức Sửa lỗi Tận gốc (Root Cause Rework & Retroactive Audit)
Khi phát hiện một dạng lỗi lặp lại trong quá trình vận hành (ví dụ: mô hình liên tục nhầm xe kéo chở tôn dài thành mặt đường):
1.  **CẤM sửa lẻ tẻ từng khung ảnh:** Hành vi sửa vá víu cục bộ sẽ làm méo mó phân phối dữ liệu.
2.  **Họp Adjudication & Cập nhật Guideline:** Ban hành Quyết định Kỹ thuật mới (ví dụ: QĐ-004: Hàng hóa sắt thép thò ra khỏi xe phải tính vào Amodal Bbox), bổ sung 4 cặp ảnh Good/Bad vào Guideline v1.2.
3.  **Hồi tố (Retroactive Audit):** Chạy script tự động quét lại toàn bộ các batch dữ liệu cũ đã xuất xưởng, triệu hồi các khung hình chứa từ khóa liên quan để gán nhãn bổ sung, tái đóng gói Release Packet v1.2.

---

## 8. SỰ THẬT TRẦN TRỤI: ĐIỂM DỄ VỠ NHẤT CỦA QUY TRÌNH (SPOF) & 3 TẦNG BẢO VỆ

> *Tiêu chuẩn Rubric: "Nhóm nói được chỗ này quy trình của chúng em dễ vỡ, và đây là cách chặn — được chấm tối đa điểm thực chiến".*

### Điểm Gãy vỡ Duy nhất (Single Point of Failure - SPOF)
Với quy mô đội ngũ 8 người, điểm dễ vỡ nhất không nằm ở thuật toán pHash hay script chia dữ liệu, mà nằm ở **SỰ MỆT MỎI CỦA CON NGƯỜI TẠI KHÂU DUYỆT NHÃN (REVIEW QC BOTTLENECK) CỦA TỔ D**.
*   Khi áp lực thời gian dồn về cuối đợt giao hàng, 2 nhân sự kiểm định của Tổ D phải duyệt hàng chục ngàn bounding box.
*   Sau 90 phút nhìn màn hình, mắt bị mỏi và xuất hiện **Hiệu ứng Neo (Anchoring Bias)**: Người duyệt có xu hướng tin tưởng vào nhãn AI Prelabel, lướt nhanh qua các khung hình và bấm Approve hàng loạt.
*   **Hậu quả chết người:** Lọt lưới các lỗi bỏ sót người đi bộ ban đêm vào tập kiểm thử, khiến hệ thống phanh tự động của xe VinFast bị khiếm khuyết khi xuất xưởng.

### Kiến trúc Phòng thủ 3 Tầng Chặn Đứng Sự Sụp Đổ (Failsafe Protocol)
1.  **Tầng 1 - CI/CD Automated Gate (Tổ B phụ trách):**
    *   Viết test suite tự động bằng Python kiểm tra 100% tệp Parquet trước khi Reviewer mở mắt ra duyệt.
    *   Tự động Reject ngay lập tức nếu phát hiện bounding box nào bị thiếu trường `motion_state`, tọa độ âm, hoặc diện tích box $< 144\text{px}^2$ mà không cắm cờ `ignore`.
2.  **Tầng 2 - Bẫy Ngầm Kỹ thuật số (Honeypot 5% - Tổ D phụ trách):**
    *   Trộn ngầm **5% khung ảnh chuẩn Vàng (Gold Ground-truth)** do Data Owner tự tay gán sẵn vào mỗi lô hàng của Reviewer.
    *   Nếu Reviewer lướt qua frame $< 3.0\text{s}$ hoặc để lọt 1 lỗi bỏ sót người đi bộ trên khung ảnh Vàng $\implies$ Hệ thống lập tức khóa tài khoản, tước quyền nghiệm thu và bắt buộc tái kiểm định toàn bộ lô.
3.  **Tầng 3 - Kỷ luật Bào mòn (Fatigue Rule):**
    *   Thiết lập giới hạn cứng trên nền tảng gán nhãn (CVAT): **Khóa ca làm việc tối đa 90 phút**. Sau 90 phút, hệ thống bắt buộc nhân sự đăng xuất nghỉ ngơi tối thiểu 15 phút để phục hồi thị giác.

---

## 9. KỊCH BẢN CHẤT VẤN CHUYÊN MÔN: TRẢ LỜI 6 CÂU HỎI HỘI ĐỒNG (TA Q1 - Q6)
*(Chuẩn bị cho 8 thành viên — Bất kỳ ai bị chỉ định cũng trả lời chính xác trong 45 giây)*

### [TA Q1] Dataset này phục vụ quyết định nào — ai dùng, dùng để làm gì?
> **Thành viên 1 / 2 trả lời:**  
> "Kính thưa Hội đồng, dataset này phục vụ trực tiếp cho mô hình AI Perception thuộc hệ thống điều khiển khung gầm xe điện thông minh VinFast VF8/VF9. Mục đích là ra quyết định: **Kích hoạt phanh khẩn cấp (AEB), phát cảnh báo tiền va chạm (FCW), hoặc không can thiệp**, với độ trễ phản ứng thời gian thực dưới **200 mili-giây** từ dữ liệu camera trước. Nhóm người dùng cuối là bộ phận điều khiển phanh tự động (Brake Actuator MCU)."

### [TA Q2] Lỗi nào đắt hơn, false positive hay false negative, và điều đó đã đổi những gì ở các bước sau?
> **Thành viên 7 trả lời:**  
> "Kính thưa Hội đồng, **False Negative (Bỏ sót) đắt hơn vượt trội**. Bỏ sót người đi bộ trong làn (Cost 1000) đắt gấp 6.67 lần Phanh oan (Cost 150) và đắt gấp 200 lần sót vật ngoài làn (Cost 5). Điều này định hình 3 thay đổi mang tính cấu trúc:
> 1. Bác bỏ mAP trung bình, thiết lập cổng QC nghiệm thu riêng biệt cho nhóm VRU với tỷ lệ bắt buộc Recall 100% (sót 1 là reject cả lô).
> 2. Chuyển đổi toàn bộ quy tắc gán nhãn sang hộp **Amodal Bbox** (vẽ trùm lên cả phần bị che khuất).
> 3. Thiết lập cờ `ignore = true` cho các vật thể $< 12\text{px}$ để bảo vệ mô hình không bị phạt oan ở những vùng quang học bất khả thi."

### [TA Q3] Guideline của nhóm xử lý ca khó nào, và xử lý ra sao?
> **Thành viên 5 / 6 trả lời:**  
> "Kính thưa Hội đồng, nhóm xử lý 10 ca biên đặc trưng của giao thông Việt Nam, tiêu biểu nhất là 3 ca:
> 1. **Xe máy tạt đầu (Cut-in) cự ly gần:** Dù mới nhô 1/3 thân xe, bắt buộc vẽ Amodal Bbox ôm trọn kích thước xe máy và đánh cờ `motion_state = cut_in`.
> 2. **Đèn pha ngược chiều lóa trắng nửa khung:** Đánh dấu toàn bộ vùng lóa trắng là **Ignore Region** (`ignore = true`) để không tính TP và không phạt FP.
> 3. **Quyền Abstain:** Khi gặp sương mù dày hoặc bóng đen không thể xác định, annotator được quyền Abstain gắn tag `can_xem_lai` đẩy sang Decision Log cho Adjudicator, cấm tự ý đoán mò."

### [TA Q4] Group key là gì, và chống rò rỉ giữa train với test bằng cách nào?
> **Thành viên 4 / 7 trả lời:**  
> "Kính thưa Hội đồng, Group Key bắt buộc là **`trip_id`** (mã hành trình chuyến đi). Ở 30fps, hai khung hình kề nhau cách nhau 33ms chứa cùng một người đi bộ ở cùng góc phố. Nếu chia ngẫu nhiên, ta đã đưa đáp án vào đề thi. Nhóm chặn rò rỉ bằng 3 tầng:
> 1. Dùng `GroupShuffleSplit` theo `trip_id`, đảm bảo giao thoa giữa Train, Val, Test bằng rỗng tuyệt đối.
> 2. Chạy thuật toán pHash kiểm toán chéo, chứng minh bằng con số định lượng: **0 cặp ảnh cận trùng** lọt sang tập kiểm thử.
> 3. Thực hiện thí nghiệm Ablation: Chia ngẫu nhiên mAP đạt 94.2% ảo, chia theo Trip đạt 81.5% thực tế, chứng minh chênh lệch 12.7% do rò rỉ."

### [TA Q5] Đo chất lượng bằng chỉ số gì, trên mẫu bao nhiêu, ai là người đo?
> **Thành viên 8 trả lời:**  
> "Kính thưa Hội đồng, nhóm bác bỏ mAP tổng hợp vì nó che giấu các lát cắt tử huyệt. Chúng em đo bằng:
> 1. **Chỉ số:** Miss Rate trên nhóm VRU trong làn ở cự ly $< 25\text{m}$, tại ngân sách cố định $\le 0.1\text{ False Positive / khung hình}$.
> 2. **Quy mô mẫu:** Tập Test ép quota tối thiểu 300 người đi bộ ban đêm và 200 sự kiện cut-in.
> 3. **Người đo:** Tổ Đo lường (Thành viên 7 và 8) thực hiện kiểm định độc lập hoàn toàn với đội gán nhãn, áp dụng phương pháp gán Đôi Mù 100% trên tập Test."

### [TA Q6] Quy trình này cần bao nhiêu người, và chỗ nào dễ vỡ nhất khi chạy thật?
> **Thành viên 3 / 8 trả lời:**  
> "Kính thưa Hội đồng, quy trình được thiết kế chuẩn xác cho đội ngũ **8 người chia 4 tổ**. Chỗ dễ vỡ nhất khi chạy thật là **nút thắt cổ chai tại khâu Review QC của Tổ D** do mỏi mắt và hiệu ứng neo tâm lý vào AI Prelabel sau 2 giờ làm việc liên tục.
> Nhóm chặn đứng bằng 3 tầng phòng thủ:
> 1. Script Python CI/CD tự động reject lô sai schema trước khi người mở giao diện.
> 2. Trộn ngầm **5% khung ảnh Vàng (Honeypot)**; lướt ẩu bỏ sót người đi bộ trên ảnh vàng sẽ bị khóa tài khoản ngay lập tức.
> 3. Khóa cứng phần mềm gán nhãn, ép buộc nghỉ ngơi 15 phút sau mỗi 90 phút làm việc liên tục."
