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

### 2.1 Khai thác Bộ Dữ liệu Nexar Collision Prediction & DADA-2000
*   **Vị trí kho dữ liệu Nexar:**
    *   Kaggle: `nexar-collision-prediction`
    *   Hugging Face Mirror: `nexar-ai/nexar_collision_prediction`
*   **Quy mô bộ dữ liệu Nexar:** 2.844 video dashcam MP4 ($1280 \times 720$ @ 30 FPS).
    *   **Tập Train (1.500 video, ~40s/video):** 750 video positive (400 va chạm thật, 350 suýt va chạm near-miss) và 750 video negative (lái bình thường).
    *   **Tập Test (1.344 video, ~10s/video):** Cắt ngắn ở các mốc `time_to_accident`: 0.5s, 1.0s, và 1.5s.
*   **TỬ HUYỆT CỦA NEXAR & GIẢI PHÁP ĐA NGUỒN (MULTI-SOURCE TOPOLOGY):**
    *   *Giới hạn thiết kế của Nexar (arXiv:2503.03848):* Nexar **chỉ tập trung vào va chạm ô tô/xe tải** ("Front-Facing Vehicle Collisions Only: Must involve cars or trucks. **Pedestrians, bicycles, motorcycles were excluded**").
    *   *Chiến lược phối hợp:* Do Cost Matrix của VinFast phạt sót VRU cực nặng (Người đi bộ 1000, Xe máy 800), nhóm bắt buộc kết hợp:
        1.  **Nexar (2.844 video):** Nguồn va chạm dương tính cho phương tiện lớn (Car / Bus / Truck).
        2.  **DADA-2000 / DoTA (2.000+ video):** Nguồn va chạm dương tính chuyên biệt cho **Người đi bộ băng đường & Xe máy tạt đầu (VRU Near-miss & Collision)**.
        3.  **In-house Dashcam VN (8h):** Dữ liệu ngõ nhỏ, mật độ xe máy kẹp 3 hỗn loạn tại Việt Nam.

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

### 3.1 Pipeline Xử lý 6 Bước & Quy trình CVAT Multi-Frame Tracking
*Nexar không gán bounding box (chỉ có nhãn thời gian video 1 điểm). Đơn vị dữ liệu của VinFast là "một vật thể trong một khung hình". Do đó, quy trình bắt buộc chuyển đổi từ Video-level sang Object-level trong CVAT:*

```text
Video Thô (Nexar/DADA/In-house, 30fps)
  ├─1. Tách Keyframe 2 fps ───────────► Giảm 15x, nhúng trip_id vào filename
  ├─2. Khử Trùng lặp (pHash) ─────────► Lọc khung đèn đỏ, copy ra output
  ├─3. ẨN DANH 2 LỚP (Face + Plate) ──► CỔNG CHẶN PHÁP LÝ (Nghị định 13)
  ├─4. AI Pre-label (Nuclio Serverless)► YOLOv10/Grounding DINO sinh candidate boxes
  ├─5. CVAT Multi-Frame Track Mode ───► Nội suy tuyến tính + Lật cờ risk_status
  └─6. Xuất Manifest Parquet & COCO ──► Đẩy vào DataLoader ML Pipeline
```

*   **Quy trình Gán nhãn Video trong CVAT (Track Mode thay vì Shape Mode):**
    1.  *Nuclio Serverless Auto-Annotation:* Triển khai YOLOv10 trên Nuclio runtime trong CVAT để sinh Bbox ban đầu cho xe cộ, người đi bộ khi confidence $> 0.6$.
    2.  *Keyframing & Linear Interpolation (TransT/SAM2):* Annotator chỉ cần căn chỉnh Bbox ở frame $t=0$ và frame $t=30$. CVAT tự động nội suy tọa độ Bbox cho 29 frame ở giữa, giảm 90% công sức vẽ tay.
    3.  *Lật cờ Thuộc tính Động học (Timeline State Flipping):* Trên thanh timeline của Track ID vật thể, annotator căn cứ vào tín hiệu visual/Nexar alert để lật cờ:
        `risk_status: normal` $\implies$ `threatening (onset va chạm)` $\implies$ `colliding (thời điểm đâm)`.

### 3.2 Đặc tả Thiết kế Kỹ thuật & Hợp đồng Dữ liệu (Pipeline Architecture & Data Contracts)

#### Giai đoạn 1: Trích xuất Khung hình & Bảo tồn Khóa Nhóm (Keyframe Extraction & Group Key Invariant)
*   **Mục tiêu Thiết kế:** Hạ mẫu thời gian để tối ưu hóa $15\times$ chi phí truyền dẫn và lưu trữ, đồng thời bảo toàn định danh chuyến đi.
*   **Hợp đồng Đầu vào (Input Contract):** Các luồng video thô `.mp4` từ Nexar, DADA-2000 và In-house Dashcam VN ($1280 \times 720$ @ 30 FPS, H.264/H.265).
*   **Quy tắc Xử lý:**
    *   Tần số lấy mẫu: Cố định $2\text{ fps}$ ($\Delta t = 500\text{ms}$).
    *   Quy chuẩn Định danh: Cấu trúc tên tệp bắt buộc nhúng Group Key: `{trip_id}_{frame_index:06d}.jpg`.
*   **Hợp đồng Đầu ra (Output Contract):** Kho khung hình JPEG chất lượng cao ($q \ge 95$), mỗi tệp liên kết bất biến với `trip_id`.

#### Giai đoạn 2: Khử Trùng lặp Nhận thức (Perceptual Hash Deduplication)
*   **Mục tiêu Thiết kế:** Loại bỏ các khung hình tĩnh do xe dừng đèn đỏ hoặc tắc đường kéo dài (tránh overfit bối cảnh nền và lãng phí 80% ngân sách gán nhãn).
*   **Thuật toán & Tham số Vận hành:**
    *   Biến đổi ảnh xám $32 \times 32 \to$ Biến đổi Cosine rời rạc (DCT) $8 \times 8 \to$ Tạo chuỗi băm 64-bit (pHash).
    *   Bộ đệm trượt cục bộ: Duy trì danh sách băm của 30 khung hình gần nhất trong cùng một `trip_id` (chống rò rỉ biên giữa các chuyến đi).
    *   Ngưỡng loại bỏ: Khoảng cách Hamming $d_H(h_t, h_{t-k}) \le 8$ với mọi $k \in [1, 30] \implies$ DROP khung hình.
*   **Hợp đồng Đầu ra:** Thư mục `filtered_frames/` chứa tập khung hình động lực học hữu ích.

#### Giai đoạn 3: Cổng Chặn Ẩn danh PII Hai Lớp & Chỉ số Rủi ro Quyền Riêng tư
*   **Mục tiêu Thiết kế:** Tuân thủ tuyệt đối Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân; cấm chuyển dữ liệu ra bên ngoài khi chưa khử khuẩn PII.
*   **Cấu trúc 2 Lớp Độc lập:**
    *   *Lớp 1 (Khuôn mặt):* Mô hình trinh sát khuôn mặt (CenterFace), mở rộng biên bounding box thêm $10\%$ theo mọi hướng để chống hụt mép, áp dụng bộ lọc mờ Gauss ($\sigma=15$, kernel $51 \times 51$).
    *   *Lớp 2 (Biển số xe):* Bộ phát hiện biển số chuyên dụng (Plate Detector), cô lập vùng ký tự và làm mờ vĩnh viễn không thể đảo ngược.
*   **Chỉ số Đo lường Bảo mật (PII Miss Rate Formula):**
    $$\text{Miss Rate}_{\text{PII}} = \frac{\text{Số vùng PII bị sót}}{\text{Tổng số vùng PII thực tế trong mẫu audit}} \times 100\%$$
*   **Ma trận Giám sát Slice-Level Bắt buộc (Detector suy thoái ở vùng tối):**
    *   *Theo phân loại PII:* Giám sát độc lập $\text{Miss Rate}_{\text{Face}} \le 0.001\%$ và $\text{Miss Rate}_{\text{Plate}} \le 0.001\%$.
    *   *Theo lát cắt môi trường:* Bắt buộc đo kiểm riêng cho **Ban đêm (Night)** và **Trời mưa (Rain)** do các detector quang học thường suy giảm độ chính xác từ $15-30\%$ trong điều kiện này.

#### Giai đoạn 4: Phân tách Tập dữ liệu Độc lập (Stratified Group Split Architecture)
*   **Mục tiêu Thiết kế:** Triệt tiêu hoàn toàn hiện tượng Rò rỉ Dữ liệu Thời gian (Temporal Leakage) — nguyên nhân chính khiến điểm **mAP tăng cao giả tạo**.
*   **Nguyên lý Phân tách:** Áp dụng thuật toán chia theo cụm (Group-based Splitting). Khóa toàn bộ các khung hình của cùng một `trip_id` vào duy nhất 1 tập con.
*   **Tỷ lệ Phân bổ:** Huấn luyện (Train $70\%$), Kiểm định (Validation $15\%$), Đánh giá (Test $15\%$).
*   **Điều kiện Tiên quyết Bất biến (Set-Theoretic Invariants):**
    $$\text{Trips}_{\text{Train}} \cap \text{Trips}_{\text{Val}} = \emptyset, \quad \text{Trips}_{\text{Train}} \cap \text{Trips}_{\text{Test}} = \emptyset, \quad \text{Trips}_{\text{Val}} \cap \text{Trips}_{\text{Test}} = \emptyset$$
*   **Kiểm toán CI/CD Tự động:** Script xác thực tự động kiểm tra tính rời nhau (disjoint) của tập hợp ID và kiểm toán chéo pHash giữa Train và Test trước khi cấp phép lưu kho.

#### Giai đoạn 5: Bảng Đối Soát Chuyển Đổi Định Dạng 5 Chỉ Số (Loss-Check Table)
Khi chuyển đổi dữ liệu từ định dạng CVAT XML sang Apache Parquet và COCO JSON, hệ thống bắt buộc đối chiếu **5 chỉ số tại cả đầu vào và đầu ra** để đảm bảo không thất thoát nhãn:

| STT | Chỉ số Kiểm soát (Loss-Check Metric) | Định dạng Nguồn (CVAT XML) | Định dạng Đích (Parquet/COCO) | Điều kiện Dung sai (Tolerance Gate) |
| :---: | :--- | :---: | :---: | :---: |
| **1** | **Tổng số lượng Shape / Object** | $N_{\text{in}}$ | $N_{\text{out}}$ | **Chênh lệch = 0** ($N_{\text{in}} = N_{\text{out}}$) |
| **2** | **Tổng số lượng Track ID duy nhất**| $T_{\text{in}}$ | $T_{\text{out}}$ | **Bảo toàn 100%** định danh quỹ đạo |
| **3** | **Số lượng Class & Tính nhất quán Class Map**| 6 classes | 6 classes | Khớp 1:1, không có nhãn bị map sang `null` |
| **4** | **Số lượng Thuộc tính có giá trị (Populated)**| $A_{\text{in}}$ | $A_{\text{out}}$ | 100% Bbox có đủ `lane`, `motion`, `risk` |
| **5** | **Số lượng Frame có ít nhất một nhãn**| $F_{\text{in}}$ | $F_{\text{out}}$ | Không rụng frame sau chuyển đổi |

### 3.3 Hợp Đồng Dữ Liệu & Schema Manifest Định Dạng Parquet (24 Trường Chuẩn Hóa)
Lưu trữ dạng Flat Table trên Apache Parquet (mã hóa Snappy, chunk size 64MB) để truy vấn siêu tốc qua Polars/DuckDB, phục vụ phân tích slice-based evaluation hàng triệu vật thể trong vài phần mười giây:

| Nhóm Trường | Tên Cột (Field Name) | Kiểu Dữ Liệu (Parquet/Arrow) | Ràng Buộc (Constraints) | Miền Giá Trị (Allowed Domain) & Ý Nghĩa Kỹ Thuật ADAS |
| :--- | :--- | :--- | :--- | :--- |
| **1. Định danh & Tọa độ Ảnh (Image-level)** | `trip_id` | `string` | NOT NULL | Định danh hành trình liên tục (**Group Key bất biến** chống rò rỉ dữ liệu). |
| | `frame_id` | `string` | NOT NULL | Mã khung hình duy nhất: `{trip_id}_{frame_idx:06d}` (ví dụ: `trip_0042_000120`). |
| | `timestamp_sec` | `float32` | NOT NULL, $\ge 0.0$ | Thời điểm khung hình tính từ đầu chuyến đi (bước nhảy $\Delta t = 0.5\text{s}$ ở 2fps). |
| | `img_path` | `string` | NOT NULL | Đường dẫn tương đối lưu trữ file ảnh: `frames/{trip_id}/{frame_id}.jpg`. |
| | `img_w` | `int16` | Cố định = 1280 | Chiều rộng ảnh chuẩn hóa (pixel). |
| | `img_h` | `int16` | Cố định = 720 | Chiều cao ảnh chuẩn hóa (pixel). |
| **2. Hình Học Bounding Box** | `obj_id` | `int32` | NOT NULL, $\ge 0$ | ID vật thể duy nhất xuyên suốt các khung hình của một trip (**Tracking ID**). |
| | `class_name` | `string` | NOT NULL | Danh mục 6 lớp: `[Pedestrian, Bicycle, Motorcycle, Car, Bus_Truck, UNKNOWN_OBJECT]`. |
| | `x_min` | `float32` | $0.0 \le x < 1280.0$ | Tọa độ pixel mép trái hộp bao. |
| | `y_min` | `float32` | $0.0 \le y < 720.0$ | Tọa độ pixel mép trên hộp bao. |
| | `w` | `float32` | $w > 0.0, x + w \le 1280$ | Chiều rộng Bounding Box (pixel). |
| | `h` | `float32` | $h > 0.0, y + h \le 720$ | Chiều cao Bounding Box (pixel). |
| **3. Che Khuất & Mép Biên** | `occlusion_level` | `string` | NOT NULL | Mức độ che khuất hình học: `[0-25%, 25-50%, 50-80%, >80%]`. |
| | `truncated` | `bool` | NOT NULL | `True` nếu vật thể bị cắt ngang bởi mép ảnh camera và phần thấy $\ge 25\%$. |
| **4. Động Học & Quyết Định Phanh** | `lane_relation` | `string` | NOT NULL | Vị trí không gian so với xe: `[in_lane, near_lane, out_of_lane]`. |
| | `distance_band` | `string` | NOT NULL | Phân tầng cự ly phanh: `[lt10m, 10to25m, gt25m]`. |
| | `motion_state` | `string` | NOT NULL | Trạng thái chuyển động vector: `[static, along, crossing, cut_in]`. |
| | `risk_status` | `string` | NOT NULL | Nguy cơ va chạm timeline: `[normal, threatening, colliding, post_crash]`. |
| **5. Cờ Kiểm Soát & Zero-Loss** | `ignore` | `bool` | NOT NULL, Default False| Cờ Zero-loss ($Loss = 0$): Bbox $< 12\text{px}$, lóa trắng photon, che khuất $> 80\%$. |
| | `prelabel_modified`| `bool` | NOT NULL | `True` nếu annotator có chỉnh sửa Bbox/thuộc tính từ đề xuất của AI Pre-label. |
| **6. Metadata Môi Trường & Audit** | `lighting` | `string` | NOT NULL | Điều kiện ánh sáng: `[daylight, twilight, night_lit, night_dark, glare]`. |
| | `weather` | `string` | NOT NULL | Điều kiện thời tiết: `[clear, cloudy, rain, fog]`. |
| | `annotator_id` | `string` | NOT NULL | Mã định danh chuyên viên gán nhãn chịu trách nhiệm (ví dụ: `ANN_005`). |
| | `qc_status` | `string` | NOT NULL | Trạng thái duyệt lô: `[pending, approved, rejected, escalated]`. |
| | `anon_version` | `string` | NOT NULL | Phiên bản thuật toán khử khuẩn PII (ví dụ: `deface_v1.2_plate_v2.0`). |

---

## 4. TỔ C: BẢN ĐẶC TẢ CHI TIẾT NHÃN (LABEL SPECS), THUỘC TÍNH (ATTRIBUTE SPECS) & BỘ 10 CA BIÊN

### 4.1 Quy Trình Thử Nghiệm Pilot Kép Trên 30 Ảnh Ca Khó (Pilot Gate)
Trước khi giải ngân ngân sách và mở gán nhãn hàng loạt cho hàng ngàn video, 2 chuyên viên Tổ C phải thực hiện gán nhãn **Độc lập Mù đôi (Double-blind Independent Annotation)** trên tập **30 khung hình stress-test** (đêm mưa ngập phản quang, đèn pha rọi ngược chiều, xe máy lách ngõ khuất cự ly $< 10\text{m}$).
*   **Chỉ số Đo lường Đồng thuận Thuộc tính (Cohen's Kappa $\kappa$):**
    Đo lường độ nhất quán trên toàn bộ 6 thuộc tính phân loại (`class_name`, `lane_relation`, `distance_band`, `motion_state`, `occlusion_level`, `risk_status`):
    $$\kappa = \frac{P_o - P_e}{1 - P_e}$$
    *(Trong đó $P_o$ là tỷ lệ quan sát thống nhất, $P_e$ là tỷ lệ thống nhất ngẫu nhiên kỳ vọng).*
*   **Chỉ số Đo lường Đồng thuận Hình học:** Mean Intersection-over-Union ($\text{mIoU}$) trên toàn bộ Bounding Box giữa 2 annotator:
    $$\text{mIoU} = \frac{1}{N}\sum_{i=1}^{N}\frac{\text{Area}(B_1^{(i)} \cap B_2^{(i)})}{\text{Area}(B_1^{(i)} \cup B_2^{(i)})}$$
*   **Ngưỡng Nghiệm thu Cổng Pilot (Pilot Hard Gate):**
    *   $\kappa \ge 0.85$ trên tất cả các trường thuộc tính.
    *   $\text{mIoU} \ge 0.70$ trên các hộp Bounding Box của cùng một cá thể vật thể.
*   **Giao thức Xử lý Khi Vi phạm Gate ($\kappa < 0.85$ hoặc $\text{mIoU} < 0.70$):**
    Đình chỉ lập tức toàn bộ hoạt động gán nhãn. Tổ trưởng Tổ C cùng Data Owner triệu tập phiên Adjudication khẩn cấp, phân tích từng điểm bất đồng, ban hành phiên bản cập nhật Guideline v1.X, ghi vào `decision_log.md` và tổ chức Pilot vòng 2 trên 30 ảnh mới.

---

### 4.2 Bản Đặc Tả Danh Mục Nhãn Vật Thể (Labels Taxonomy & Class Specifications)

Trong hệ thống ADAS/AEB xe điện VinFast, nhãn không chỉ đơn thuần là tên gọi, mà là **đặc trưng hình học đại diện cho khối thể tích va chạm vật lý (Physical Collision Footprint)**. Toàn bộ 6 lớp được chuẩn hóa triệt để:

```
                                 HỆ THỐNG PHÂN LOẠI 6 LỚP VẬT THỂ (ADAS TAXONOMY)
  ┌───────────────────────────────────────────────────────┬───────────────────────────────────────────────────────┐
  │         NHÓM ĐỐI TƯỢNG YẾU THẾ (VRU - Cost 800 - 1000)│             NHÓM PHƯƠNG TIỆN CƠ GIỚI & VẬT CẢN        │
  ├───────────────────────────┬───────────────────────────┼───────────────────────────┬───────────────────────────┤
  │ 1. Pedestrian (Người đi bộ)│ 2. Motorcycle (Xe máy)    │ 3. Bicycle (Xe đạp)       │ 4. Car (Ô tô con)         │
  │ • Người đi, chạy, cúi, ngã│ • Xe số, ga, côn, xe điện │ • Xe đạp cơ, xe đạp điện  │ • Sedan, SUV, CUV, Bán tải│
  │ • Người đẩy xe nôi, xe lăn│ • Kẹp 2, kẹp 3, áo mưa    │ • Gộp người + xe làm một  │ • Đáy lốp đến nóc/giá chở │
  ├───────────────────────────┴───────────────────────────┼───────────────────────────┴───────────────────────────┤
  │ 5. Bus_Truck (Xe buýt, Khách, Tải, Đầu kéo, Chuyên dụng)│ 6. UNKNOWN_OBJECT (Chướng ngại vật bất thường gầm xe) │
  │ • Xe buýt VinBus, xe tải thùng, xe bồn, container     │ • Lốp vỡ, đá tảng, cành cây, dải phân cách xô lệch    │
  │ • BẮT BUỘC bao trùm sắt thép/tôn thò ra khỏi thùng xe │ • Động vật lớn (chó, trâu, bò) băng qua đường         │
  └───────────────────────────────────────────────────────┴───────────────────────────────────────────────────────┘
```

#### Chi Tiết Quy Chuẩn Từng Lớp Nhãn:

#### 1. Lớp `Pedestrian` (Người đi bộ & Người di chuyển yếu thế)
*   **Mã lớp / ID:** `Pedestrian` (Class ID: `0`).
*   **Phạm vi bao hàm (Inclusion Criteria):**
    *   Con người ở mọi tư thế: đi bộ, chạy bộ, đứng chờ, cúi nhặt đồ, ngồi trên vỉa hè/lề đường, trượt chân ngã hoặc nằm trên mặt đường.
    *   Người khuyết tật di chuyển bằng xe lăn (xe lăn tay hoặc xe lăn điện tự hành).
    *   Người đi bộ đang đẩy xe nôi trẻ em hoặc xe đẩy hàng cá nhân (nếu gắn sát cơ thể $< 0.3\text{m}$).
    *   Công nhân vệ sinh môi trường, công nhân sửa đường, cảnh sát giao thông đứng điều tiết.
*   **Quy tắc vẽ Bounding Box (Amodal 2D Bbox):**
    *   Hộp chữ nhật đứng bao trọn từ đỉnh đầu (hoặc chóp mũ bảo hiểm, nón lá, mũ rộng vành) xuống điểm thấp nhất của gót giày/mặt đế tiếp xúc với mặt đường.
    *   **Vật dụng mang theo:** Bắt buộc bao trọn ba lô đeo sau lưng, túi xách mang bên hông, ô dù đang giương che mưa/nắng, gậy chống của người già.
    *   Trường hợp đẩy xe nôi: Bao trọn cả người đẩy và xe nôi thành một hộp `Pedestrian` duy nhất (tránh tách rời khiến hệ thống hiểu nhầm khoảng cách trống giữa người và xe).
*   **Điểm tiếp đất (Ground Contact Point):**
    *   Trung điểm của đáy hộp Bbox: $(x_g, y_g) = (x_{\text{min}} + w/2, y_{\text{min}} + h)$. Đây là điểm neo hình học để ma trận biến đổi Inverse Perspective Mapping (IPM) ánh xạ sang hệ tọa độ xe (Ego Coordinates) tính cự ly khoảng cách thực tế.
*   **Kích thước & Ngưỡng phân giải:** Chiều cao tối thiểu $h \ge 12\text{px}$. Nếu $h < 12\text{px} \implies$ Đánh cờ `ignore = true`.

#### 2. Lớp `Motorcycle` (Xe máy, Xe mô tô, Xe máy điện)
*   **Mã lớp / ID:** `Motorcycle` (Class ID: `1`).
*   **Phạm vi bao hàm:** Xe máy số, xe tay ga, xe tay côn, mô tô phân khối lớn, xe máy điện (VinFast Klara, Feliz, Evo200, Vento, Theon...), xe đạp điện không có bàn đạp trợ lực.
*   **Quy tắc vẽ Bounding Box (Kinematic Footprint Invariant):**
    *   **CẤM TUYỆT ĐỐI tách riêng nhãn `rider` và nhãn `motorcycle`.** Hệ thống phanh tự động cần nhận biết một thực thể vật lý nguyên khối có cùng vector động học và cùng vùng chiếm chỗ không gian.
    *   Hộp bao trọn người điều khiển, toàn bộ hành khách ngồi sau (kẹp 2, kẹp 3, trẻ em), gương chiếu hậu 2 bên, ống bô, giỏ xe, và hai điểm tiếp đất của bánh trước/bánh sau.
    *   **Đặc thù Áo mưa cánh dơi:** Nếu người lái phủ áo mưa cánh dơi trùm qua đầu xe và tà áo bay phấp phới, vẽ Bbox bao trọn diện tích tà áo nếu độ mở rộng cơ học $\ge 10\text{cm}$ so với thân xe (vì tà áo mưa có thể gây va quẹt hoặc che khuất cảm biến).
    *   **Đặc thù Chở hàng cồng kềnh Việt Nam:** Xe máy chở sọt hoa quả, bình gas, thùng carton, tủ lạnh, giá sắt phía sau $\implies$ **BẮT BUỘC mở rộng Bbox bao trùm 100% khối hàng hóa**. Khối hàng này là diện tích va chạm thực tế đe dọa đầu xe VinFast.
    *   Xe máy đỗ không người lái bên đường: Vẫn gán nhãn `Motorcycle`.
*   **Điểm tiếp đất:** Trung điểm đoạn nối giữa hai điểm đáy lốp xe tiếp xúc mặt đường.
*   **Kích thước tối thiểu:** $w \ge 12\text{px}, h \ge 15\text{px}$.

#### 3. Lớp `Bicycle` (Xe đạp, Xe đạp trợ lực điện)
*   **Mã lớp / ID:** `Bicycle` (Class ID: `2`).
*   **Phạm vi bao hàm:** Xe đạp thông thường, xe đạp địa hình, xe đạp mini, xe đạp trợ lực điện (có bàn đạp cơ), xe đạp chở hàng 2 bánh thô sơ.
*   **Quy tắc vẽ Bounding Box:**
    *   Tương tự xe máy: **Gộp người đạp xe và chiếc xe đạp thành 1 Bbox duy nhất**. Hộp bao trùm từ đỉnh đầu người đạp đến đáy lốp xe, bao gồm tay lái, bàn đạp và gác-ba-ga.
    *   Xe đạp rỗng không người lái dựng ở lề đường: Gán `Bicycle`.
*   **Điểm tiếp đất:** Trung điểm đoạn thẳng nối đáy 2 bánh xe.
*   **Kích thước tối thiểu:** $w \ge 10\text{px}, h \ge 12\text{px}$.

#### 4. Lớp `Car` (Xe ô tô con, Xe chở người $\le 9$ chỗ, Bán tải)
*   **Mã lớp / ID:** `Car` (Class ID: `3`).
*   **Phạm vi bao hàm:** Sedan, Hatchback, SUV, CUV, MPV, xe bán tải (Pickup), taxi, xe cảnh sát tuần tra cỡ nhỏ, xe cứu thương cỡ nhỏ 4-9 chỗ.
*   **Quy tắc vẽ Bounding Box:**
    *   Bao kín toàn bộ 4 cạnh ngoại vi thân xe: mép ngoài cùng của gương chiếu hậu 2 bên, mép lốp xe tiếp đất, cản trước/sau, nóc xe.
    *   **Giá chở hàng trên nóc (Roof-rack / Roof-box):** Nếu xe có gắn giá chở hành lý hoặc vali trên nóc, Bbox phải bao trọn cả khối hành lý trên nóc xe.
    *   Không tính ăng-ten dạng sợi mảnh kéo dài hoặc khói xả từ ống bô.
*   **Điểm tiếp đất:** Trọng tâm diện tích tiếp xúc của các lốp xe nhìn thấy với mặt đường.
*   **Kích thước tối thiểu:** $w \ge 15\text{px}, h \ge 12\text{px}$.

#### 5. Lớp `Bus_Truck` (Phương tiện thương mại, Vận tải nặng & Chuyên dụng)
*   **Mã lớp / ID:** `Bus_Truck` (Class ID: `4`).
*   **Phạm vi bao hàm:** Xe buýt nội đô (VinBus điện, xe buýt liên tỉnh), xe khách giường nằm, xe tải nhẹ, xe tải nặng, xe bồn chở xăng dầu, xe trộn bê tông, xe ben chở cát đá, xe đầu kéo kéo sơ-mi rơ-moóc container, xe quét rác, xe cẩu/máy xúc đang tự hành trên đường.
*   **Quy tắc vẽ Bounding Box & Ca Nguy Cấp Sắt Thép Thò Ra Đuôi:**
    *   Bao trùm toàn bộ kết cấu hình học của thân xe: gương chiếu hậu bản lớn, nóc cabin/thùng xe, lốp xe tiếp đất, thang leo, cản sau.
    *   **QUY TẮC SỐNG CÒN (Hàng thò ra đuôi xe):** Xe tải chở thép cây, ống nhựa tiền phong, cọc bê tông, dầm thép hoặc tôn cuộn thò ra ngoài đuôi thùng xe hàng mét $\implies$ **BẮT BUỘC vẽ Bbox bao trọn toàn bộ chiều dài phần hàng hóa thò ra**. Đây là nguyên nhân trực tiếp của các vụ tai nạn đâm thủng kính lái (Penetration Under-ride Crash).
    *   **Xe Container Gập Góc (Jack-knifing):**
        *   Nếu xe chạy thẳng trên cùng trục đường: Gán **1 Bbox duy nhất** ôm trọn đầu kéo và rơ-moóc.
        *   Nếu xe đang cua gập góc chữ L tại ngã tư hoặc quay đầu: Bắt buộc tách thành **2 Bbox `Bus_Truck` riêng biệt** (1 Bbox cho cabin đầu kéo, 1 Bbox cho sơ-mi rơ-moóc phía sau) để tránh tạo ra Bbox khổng lồ chứa $> 70\%$ khoảng trống rỗng đánh lừa thuật toán lập quỹ đạo xe tự lái.
*   **Điểm tiếp đất:** Trung điểm vệt tiếp đất của dàn lốp sau hoặc lốp thấp nhất tiếp xúc mặt đường.
*   **Kích thước tối thiểu:** $w \ge 20\text{px}, h \ge 20\text{px}$.

#### 6. Lớp `UNKNOWN_OBJECT` (Chướng ngại vật bất thường trên mặt đường)
*   **Mã lớp / ID:** `UNKNOWN_OBJECT` (Class ID: `5`).
*   **Phạm vi bao hàm:** Toàn bộ các chướng ngại vật nằm trên lòng đường hoặc sát mép làn đe dọa va quẹt gầm xe, làm nổ lốp hoặc làm tài xế mất lái:
    *   Mảnh lốp xe tải bị nổ nát văng ra đường (Road Gator).
    *   Thùng hàng carton, két bia, bao tải nông sản rơi rớt từ xe tải phía trước.
    *   Tảng đá lớn, cành cây to gãy đổ chắn ngang đường sau mưa bão.
    *   Dải phân cách nhựa di động, cọc tiêu giao thông hình nón, thùng phuy công trình bị xô lệch nằm giữa làn xe.
    *   Động vật lớn chạy rông trên đường (chó thả rông, trâu, bò, dê...).
    *   Xe ba gác tự chế, xe lôi tự dập không đạt chuẩn kiểm định phương tiện cơ giới.
*   **Ngưỡng gán nhãn kích thước vật lý:**
    *   Chỉ gán nhãn khi vật thể có kích thước tối thiểu một chiều $\ge 20\text{cm}$ (đủ gây cạ gầm xe VF8 có khoảng sáng gầm $175\text{mm}$) và nằm trong cự ly $< 40\text{m}$.
    *   **CẤM GÁN:** Rác thải sinh hoạt nhỏ nhẹ (túi nilon, vỏ lon nước ngọt, mảnh lá rụng) có kích thước $< 10\text{cm}$.
*   **Điểm tiếp đất:** Vùng đáy thấp nhất tiếp xúc với mặt đường.

#### 7. Danh Mục Thực Thể Bị Cấm Gán Nhãn Tuyệt Đối (Negative Exclusion Criteria - CẤM GÁN)
Để triệt tiêu hiện tượng Phanh oan (False Positive - Cost 150), nhân sự gán nhãn tuyệt đối **KHÔNG ĐƯỢC VẼ BBOX** lên các đối tượng sau:
1.  **Pano, Áp phích & Biển quảng cáo ven đường:** Hình in kích thước lớn người mẫu, xe hơi, xe máy in trên biển quảng cáo tấm lớn hoặc cột pano ven phố.
2.  **Hình vẽ trang trí trên thân xe buýt / xe tải:** Tranh ảnh người đi bộ, vận động viên thể thao in trên decal dán hông xe buýt hoặc thùng xe đông lạnh. *(Quy tắc kiểm tra: Mặt phẳng chuyển động gắn liền với chuyển động của xe tải).*
3.  **Bóng đổ vật lý (Shadows):** Vệt bóng đổ dài của người đi bộ hoặc xe máy trên mặt đường nhựa do ánh đèn cao áp hoặc ánh nắng chiều chiếu xiên. *(Chỉ vẽ Bbox ôm thân người/xe, tuyệt đối không kéo đáy Bbox trùm lên bóng đổ).*
4.  **Bóng phản chiếu quang học (Specular Reflections):** Hình ảnh phản chiếu gương của người/xe trên mặt đường nhựa ướt sũng sau mưa, phản chiếu qua tường kính tòa nhà hoặc gương cầu lồi giao thông.
5.  **Ma-nơ-canh & Hình nhân tĩnh:** Ma-nơ-canh mặc quần áo đứng trong tủ kính showroom ven đường (nằm ngoài vỉa hè).

---

### 4.3 Bản Đặc Tả Thuộc Tính Kỹ Thuật (Attribute Specifications & Kinematic Semantics)

Trong hệ thống AEB xe tự lái, Bounding Box chỉ trả lời câu hỏi *"Có vật gì ở đâu?"*. Bộ thuộc tính kỹ thuật trả lời câu hỏi sống còn: *"Vật đó đang làm gì, cách bao xa, và có đâm vào xe trong 200ms tới hay không?"*. Đây là đầu vào trực tiếp cho máy trạng thái (State Machine) của bộ điều khiển phanh.

```
                  CƠ CHẾ ÁNH XẠ THUỘC TÍNH SANG BỘ ĐIỀU KHIỂN PHANH KHẨN CẤP (AEB)
  ┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
  │ [Perception Bbox] ──► [lane_relation] ──► [distance_band] ──► [motion_state] ──► [AEB Decision] │
  ├─────────────────────────────────────────────────────────────────────────────────────────────────┤
  │ • in_lane              • lt10m (< 10m)      • cut_in / crossing      ════► PHANH KHẨN CẤP (AEB)  │
  │ • near_lane            • 10to25m (10-25m)   • crossing / along       ════► CẢNH BÁO + SẠC PHANH  │
  │ • out_of_lane          • gt25m (> 25m)      • static / along         ════► THEO DÕI THỤ ĐỘNG     │
  └─────────────────────────────────────────────────────────────────────────────────────────────────┘
```

#### 1. Thuộc tính `occlusion_level` (Mức độ Che khuất Hình học)
*   **Định nghĩa toán học:** Tỷ lệ phần diện tích cơ thể/thân vỏ 2D của vật thể bị che chắn bởi vật cản khác (xe phía trước, cột điện, cây cối, dải phân cách) so với toàn bộ diện tích hộp Amodal hoàn chỉnh:
    $$\text{Occlusion Ratio} = \left(1 - \frac{\text{Area}_{\text{visible}}}{\text{Area}_{\text{amodal}}}\right) \times 100\%$$
*   **Bảng Phân loại 4 Mức Rời rạc & Hành vi Gán nhãn:**

| Giá trị (Enum) | Ngưỡng Che khuất | Dấu hiệu Nhận diện Trực quan | Hành vi Gán nhãn Bắt buộc | Tác động ML Loss |
| :--- | :---: | :--- | :--- | :---: |
| `0-25%` | $0\% \le \text{Occ} \le 25\%$ | Vật thể nhìn thấy gần như nguyên vẹn, chỉ bị che khuất nhỏ không đáng kể (mép gương, gót chân). | Vẽ Bbox sát đường biên nhìn thấy. | Loss weight = 1.0 |
| `25-50%` | $25\% < \text{Occ} \le 50\%$ | Bị che khuất một phần (ví dụ: người đi bộ bị che từ đùi trở xuống bởi nắp capo xe khác; xe máy bị che mất 1 bánh). | Vẽ Bbox amodal mở rộng theo suy luận biên vật lý. | Loss weight = 1.0 |
| `50-80%` | $50\% < \text{Occ} \le 80\%$ | Bị che khuất nghiêm trọng (chỉ nhìn thấy đầu và vai người đi bộ qua đuôi ô tô; xe máy thò đầu xe ra từ ngõ). | **BẮT BUỘC vẽ Amodal Bbox** bao trọn toàn bộ chiều cao/thể tích giả định. | Loss weight = 1.0 (VRU)<br>Loss weight = 0.5 (Xe) |
| `>80%` | $\text{Occ} > 80\%$ | Chỉ nhìn thấy một phần rất nhỏ không đặc trưng (1 bàn chân thò ra dưới gầm xe, 1 góc gương chiếu hậu). | Duy trì Bbox amodal để giữ Tracking ID nhưng **BẮT BUỘC cắm cờ `ignore = true`**. | **Zero-loss** ($Loss = 0$) |

#### 2. Thuộc tính `truncated` (Bị cắt bởi mép biên ảnh)
*   **Định nghĩa:** Cờ Boolean (`true` / `false`) biểu thị vật thể có phần thân bị cắt ngang bởi 1 trong 4 đường mép của cảm biến camera ($x=0, y=0, x=1279, y=719$) hay không.
*   **Quy chuẩn phán quyết:**
    *   `truncated = true`: Khi có ít nhất 1 cạnh của Bbox chạm mép ảnh VÀ diện tích nhìn thấy của vật thể $\ge 25\%$ thể tích thực tế.
    *   `truncated = false`: Vật thể nằm trọn vẹn $100\%$ bên trong khung hình camera.
    *   *Trường hợp biên:* Nếu vật thể chạm mép ảnh nhưng phần diện tích nhìn thấy $< 25\%$ và không thể nhận dạng chắc chắn bằng mắt thường $\implies$ Đánh cờ `ignore = true` (không phạt lỗi nhận diện ở biên quang học).

#### 3. Thuộc tính `lane_relation` (Quan hệ Không gian với Hành lang Làn xe Xe VinFast)
*   **Cơ sở Hình học & Động học Xe:**
    *   Chiều rộng thân xe VinFast VF8: $W_{\text{ego}} = 1.93\text{m}$.
    *   Hành lang An toàn Phanh (Collision Safety Corridor): Chiều rộng làn tiêu chuẩn $W_{\text{lane}} = 3.5\text{m}$ (tương đương biên độ mở rộng an toàn $\pm 0.55\text{m}$ mỗi bên tính từ mép ngoài thân xe).
*   **3 Trạng thái Phân định Tuyệt đối:**

| Giá trị (Enum) | Vị trí Không gian Vật lý | Tọa độ Ngang Tương đối ($x_{\text{lat}}$) | Quyết định Hệ thống ADAS |
| :--- | :--- | :---: | :--- |
| **`in_lane`** | Điểm tiếp đất $(x_g, y_g)$ của vật thể nằm **bên trong vạch kẻ làn xe của xe VinFast** (hoặc nằm trong dải $|x_{\text{lat}}| \le 1.75\text{m}$ tính từ trục giữa camera trước nếu đường không có vạch). | $|x_{\text{lat}}| \le 1.75\text{m}$ | **Kích hoạt logic AEB Phanh Khẩn Cấp**. Vật thể nằm trực tiếp trên quỹ đạo va chạm hình học! |
| **`near_lane`** | Điểm tiếp đất nằm ngoài làn xe nhưng nằm trong **vùng đệm an toàn rủi ro $\le 1.5\text{m}$** tính từ vạch phân làn (làn liền kề, mép lề đường, vạch dừng xe máy, lối mở dải phân cách). | $1.75\text{m} < |x_{\text{lat}}| \le 3.25\text{m}$ | **Kích hoạt Cảnh báo FCW & Nạp sẵn áp suất dầu phanh (Brake Pre-fill)**. Sẵn sàng phanh nếu vật thể đổi hướng! |
| **`out_of_lane`** | Điểm tiếp đất nằm ngoài vùng đệm rủi ro ($> 1.5\text{m}$ ngoài mép làn xe, trên vỉa hè cao, bên kia dải phân cách cứng bê tông, làn xe đối diện cách xa). | $|x_{\text{lat}}| > 3.25\text{m}$ | **Theo dõi Thụ động (Passive Monitoring)**. TUYỆT ĐỐI KHÔNG can thiệp phanh để triệt tiêu Phanh oan (Cost 150). |

#### 4. Thuộc tính `distance_band` (Phân tầng Cự ly Vật lý & Visual Proxies)
*   **Cơ sở Động lực học Phanh Thực tế (VinFast VF8 nặng ~2.6 tấn, tốc độ đô thị $50\text{ km/h} \approx 13.9\text{ m/s}$):**
    *   Thời gian xử lý dữ liệu Perception + trễ cơ cấu chấp hành phanh: $t_{\text{react}} = 200\text{ms} \implies$ Quãng đường trôi tự do $S_{\text{react}} = 13.9 \times 0.2 = 2.78\text{m}$.
    *   Gia tốc phanh khẩn cấp cực đại: $a = -0.8\text{g} \approx -7.85\text{ m/s}^2$.
    *   Quãng đường phanh thuần túy: $S_{\text{brake}} = \frac{v^2}{2|a|} = \frac{13.9^2}{2 \times 7.85} \approx 12.3\text{m}$.
    *   Tổng khoảng cách dừng xe tối thiểu trên đường khô: $S_{\text{stop}} = 2.78 + 12.3 \approx 15.1\text{m}$.
    *   Trên đường ướt mưa trơn trượt (hệ số ma sát lốp giảm xuống $\mu \approx 0.5$): $S_{\text{stop}} \approx 22.5\text{m}$.
*   **Bảng Phân Tầng Cự Ly & Thước Đo Trực Quan (Visual Proxies trên ảnh $1280 \times 720$, FOV 120°):**

| Cự ly (Enum) | Khoảng cách Thực tế | Ý nghĩa Động học Phanh | Vị trí Đáy Hộp ($y_{\text{bottom}}$) | Kích thước Hộp Chiều cao ($h_{\text{ped}}$) | Kích thước Hộp Chiều rộng ($w_{\text{car}}$) |
| :--- | :---: | :--- | :---: | :---: | :---: |
| **`lt10m`** | $< 10\text{ mét}$ | **Vùng Nguy Cấp Tử Vong (Crash Envelope):** Không đủ cự ly dừng xe; kích hoạt phanh $100\%$ lực để triệt tiêu xung lực va chạm (Mitigation). | $y_{\text{bottom}} \ge 540\text{px}$ (Nằm ở 1/4 đáy ảnh) | $h_{\text{ped}} > 140\text{px}$ | $w_{\text{car}} > 220\text{px}$ |
| **`10to25m`** | $10 - 25\text{ mét}$ | **Vùng Vàng Can Thiệp (Primary Intervention):** Đủ cự ly phanh dừng hẳn xe an toàn trước chướng ngại vật; kích hoạt AEB/FCW toàn diện. | $420\text{px} \le y_{\text{bottom}} < 540\text{px}$ | $60\text{px} \le h_{\text{ped}} \le 140\text{px}$ | $100\text{px} \le w_{\text{car}} \le 220\text{px}$ |
| **`gt25m`** | $> 25\text{ mét}$ | **Vùng Nhận Thức Tầm Xa (Tracking Horizon):** Vật thể chưa đe dọa trực tiếp; thuật toán duy trì Track ID dự đoán quỹ đạo. | $y_{\text{bottom}} < 420\text{px}$ (Nằm ở nửa trên ảnh) | $h_{\text{ped}} < 60\text{px}$ | $w_{\text{car}} < 100\text{px}$ |

#### 5. Thuộc tính `motion_state` (Trạng thái Động học Vector Đa Khung Hình)
*   **Phương pháp xác định:** So sánh vị trí tương đối và vector chuyển động của đối tượng qua chuỗi khung hình liên tiếp trên timeline CVAT ($\Delta t = 500\text{ms}$):
    *   `static` (Đứng yên): Vận tốc thực tế so với mặt đường $v < 0.5\text{ km/h}$ (dịch chuyển thực tế $\Delta d < 0.15\text{m}$ sau 500ms). Ví dụ: Xe đỗ sát vỉa hè, người đứng đợi qua đường, thùng carton rơi trên mặt đường.
    *   `along` (Di chuyển dọc làn): Hướng chuyển động song song với trục đường ($\pm 30^\circ$ so với vạch kẻ làn), độ dịch chuyển ngang không đáng kể ($|v_{\text{lat}}| < 0.3\text{m/s}$). Ví dụ: Ô tô đang chạy cùng chiều phía trước, xe máy đi cùng chiều trong làn.
    *   `crossing` (Cắt ngang hành lang di chuyển): Vector vận tốc có thành phần vuông góc hoặc chéo góc rõ rệt với trục đường ($v_{\text{lat}} \ge 0.5\text{m/s}$). Ví dụ: Người đi bộ băng qua đường, xe đạp rẽ ngang qua ngã tư.
    *   `cut_in` (Tạt đầu đột ngột vào làn): Phương tiện từ làn liền kề hoặc góc khuất chuyển hướng đột ngột lấn vào hành lang an toàn làn xe ($|x_{\text{lat}}| \le 1.75\text{m}$) ở khoảng cách gần ($< 25\text{m}$) với tốc độ lấn ngang $v_{\text{lat}} \ge 1.0\text{m/s}$. **Đây là kịch bản nguy hiểm nhất trong giao thông Việt Nam $\implies$ Kích hoạt chuông báo FCW và nạp phanh khẩn cấp.**

#### 6. Thuộc tính `risk_status` (Trạng thái Nguy cơ Trên Trục Thời Gian - CVAT Timeline)
Thuộc tính đồng bộ trực tiếp với mốc thời gian va chạm `time_to_accident` ($TTC$) trong bộ dữ liệu chuẩn Nexar và DADA-2000:
*   `normal`: Trạng thái an toàn. Thời gian dự kiến va chạm $TTC > 3.0\text{s}$ hoặc quỹ đạo hai bên không giao cắt.
*   `threatening`: Trạng thái đe dọa va chạm trực diện. $TTC \le 2.0\text{s}$, vật thể nằm trong hành lang di chuyển mà không có dấu hiệu giảm tốc hay tránh đường.
*   `colliding`: Thời điểm tiếp xúc cơ học va chạm ($TTC < 0.3\text{s}$ hoặc khung hình đâm va trực tiếp).
*   `post_crash`: Các khung hình sau thời điểm va chạm ($t > t_{\text{accident}}$), nhận diện nguy cơ thứ cấp như xe bị văng xoay ngang, nạn nhân ngã trên mặt đường.

#### 7. Cờ `ignore` (Cờ Bỏ qua Tính Loss / Zero-Loss Mask)
*   **Bản chất toán học:** Cờ nhị phân (`true` / `false`). Khi `ignore = true`, Bbox này bị triệt tiêu hoàn toàn khỏi hàm mất mát huấn luyện:
    $$\mathcal{L}_{\text{total}} = \sum_{i=1}^{M} (1 - \text{ignore}_i) \cdot \mathcal{L}_{\text{det}}(B_i, \hat{B}_i)$$
    Đồng thời, đối tượng này **không tính là True Positive và KHÔNG BỊ PHẠT khi mô hình bỏ sót hoặc phát hiện nhầm (False Positive / False Negative)**.
*   **5 Trường hợp Bắt buộc Kích hoạt `ignore = true` (Strict Rules):**
    1.  Vật thể có chiều cao Bbox $h < 12\text{px}$ (ở cự ly $> 80\text{m}$, số lượng photon không đủ để thuật toán quang học trích xuất đặc trưng).
    2.  Lóa sáng bão hòa cảm biến: Vùng Bbox có $> 80\%$ diện tích pixel chạm ngưỡng trắng tuyệt đối ($Pixel = 255$) do đèn pha xe đối diện chiếu thẳng vào camera.
    3.  Che khuất nghiêm trọng $> 80\%$ (`occlusion_level = >80%`) mà không có chuỗi theo dõi mượt mà từ các khung hình trước đó.
    4.  Vết nhòe quang học do giọt nước mưa lớn hoặc bùn đất bám trực tiếp trên kính chắn gió che khuất $> 50\%$ vật thể phía sau.
    5.  Cụm đám đông người đi bộ hoặc xe máy chen chúc dính chặt thành một khối không thể phân tách đường biên cá thể (Ca biên 7).

#### 8. Cờ `prelabel_modified` (Cờ Giám sát Thiên kiến Neo AI - Anchoring Bias)
*   `true`: Chuyên viên gán nhãn có can thiệp thủ công (điều chỉnh tọa độ Bbox $\ge 5\text{px}$, thay đổi lớp nhãn `class_name`, hoặc chỉnh sửa bất kỳ trường thuộc tính nào) so với kết quả AI Pre-label đề xuất ban đầu.
*   `false`: Chuyên viên gán nhãn bấm chấp nhận nguyên bản $100\%$ kết quả của AI.
*   **Ý nghĩa Giám sát:** Hệ thống telemetry tính toán tỷ lệ can thiệp $\text{PMR} = \frac{\sum \text{modified}}{\sum \text{total}}$. Nếu một chuyên viên có $\text{PMR} < 5\%$ trên một lô ảnh khó, hệ thống tự động gắn cờ cảnh báo nghi vấn lười duyệt ẩu (Anchoring Bias) và kích hoạt đợt tái thẩm định ngẫu nhiên của Tổ D.

#### 9. Metadata Bối Cảnh Môi Trường (Image-Level Metadata Specs)
*   `lighting` (Điều kiện ánh sáng khung hình):
    *   `daylight`: Ban ngày trời sáng rõ (độ rọi quang học $E > 1000\text{ lux}$).
    *   `twilight`: Hoàng hôn, chập tối, rạng đông ($10 \le E \le 1000\text{ lux}$).
    *   `night_lit`: Ban đêm có hệ thống đèn đường đô thị chiếu sáng liên tục ($E < 10\text{ lux}$, có nguồn sáng nhân tạo).
    *   `night_dark`: Ban đêm tối hoàn toàn, không có đèn đường (vùng ngoại ô/nông thôn, chỉ có đèn pha xe).
    *   `glare`: Hiện tượng lóa quang học nghiêm trọng (mặt trời chiếu xiên thẳng góc vào camera hoặc đèn pha pha xa xe đối diện rọi thẳng).
*   `weather` (Điều kiện thời tiết):
    *   `clear`: Trời trong xanh, không mưa, đường khô ráo.
    *   `cloudy`: Trời nhiều mây râm mát, ánh sáng khuếch tán đều.
    *   `rain`: Trời mưa (cần gạt nước kính lái hoạt động, mặt đường đọng nước phản chiếu ánh đèn).
    *   `fog`: Sương mù dày đặc hoặc khói đốt rơm rạ làm giảm tầm nhìn xa dưới $100\text{m}$.
*   `annotator_id`: Mã định danh nhân sự gán nhãn (ví dụ: `ANN_005`).
*   `qc_status`: Trạng thái kiểm định lô: `pending` (chờ duyệt), `approved` (đã duyệt), `rejected` (bị từ chối), `escalated` (chuyển hội đồng phân xử).
*   `anon_version`: Mã phiên bản khử khuẩn PII làm mờ khuôn mặt và biển số (ví dụ: `deface_v1.2_plate_v2.0`).

---

### 4.4 Quy Tắc Che Khuất (Occlusion) & Hộp Amodal Vật Lý

*   **Triết lý Amodal Bbox Cốt lõi:** Hệ thống phanh tự động (AEB) của xe VinFast can thiệp vật lý để tránh va chạm với **thực thể vật chất khách quan**, chứ không phải với các pixel quang học nhìn thấy được. Khi xe máy bị che khuất một nửa sau đuôi xe tải, phần đuôi xe máy đó vẫn tồn tại và vẫn có thể đâm vỡ cản trước xe VinFast.
*   **Nguyên tắc Thao tác:** Annotator phải quan sát hình thái đối tượng, đối chiếu các phần cơ thể/vỏ xe nhìn thấy, suy đoán kích thước tiêu chuẩn của lớp đối tượng để vẽ khung bao trùm toàn bộ thể tích vật lý thực tế.

| Phân Loại Mức Độ Che Khuất | Đối Tượng Yếu Thế (VRU: `Pedestrian`, `Motorcycle`, `Bicycle`) | Phương Tiện Lớn (`Car`, `Bus_Truck`, `UNKNOWN_OBJECT`) |
| :--- | :--- | :--- |
| **0 – 25% (Rõ ràng)** | Gán Bbox bao sát viền ngoài nhìn thấy. Thuộc tính `occlusion_level = 0-25%`. | Gán Bbox bình thường. Thuộc tính `occlusion_level = 0-25%`. |
| **25 – 50% (Che khuất vừa)** | Bắt buộc vẽ **Amodal Bbox** suy luận phần bị che. Cờ `occlusion_level = 25-50%`. | Vẽ Amodal Bbox theo hình khối thân xe. Cờ `occlusion_level = 25-50%`. |
| **50 – 80% (Che khuất nặng)** | **Bắt buộc vẽ Amodal Bbox**. Dựa vào vị trí đầu/vai hoặc bánh xe suy ra toàn bộ cơ thể. Cờ `occlusion_level = 50-80%`. Loss tính đủ $100\%$. | Vẽ Amodal Bbox nếu nhận biết được $\ge 1$ đặc trưng cấu trúc xe (đèn, biển số, nóc). Loss weight = 0.5. |
| **> 80% (Che khuất cực độ)** | Vẽ Amodal Bbox duy trì Track ID, **BẮT BUỘC cắm cờ `ignore = true`**. Không tính loss, không phạt model. | Vẽ Amodal Bbox duy trì Track ID, **BẮT BUỘC cắm cờ `ignore = true`**. |

---

### 4.5 Cơ Chế Vùng Bỏ Qua (Ignore Region) & Giao Thức Hoãn Phán Quyết (Abstain Protocol)

*   **Bản chất Kỹ thuật của Ignore Region:**
    Hệ thống camera có những giới hạn vật lý không thể vượt qua: cự ly quá xa khiến vật thể chỉ chiếm vài pixel, hoặc ánh sáng ngược cực mạnh làm toàn bộ photodiode của cảm biến bão hòa điện tích (Saturation/Clipping ở giá trị 255). Nếu ép mô hình nhận diện những vùng này, mô hình sẽ học các đặc trưng rác (noise artifacts); ngược lại nếu phạt mô hình khi bỏ sót, hàm mất mát sẽ bị mất ổn định. Giải pháp là định nghĩa vùng **Ignore Region**:
    *   Bbox gắn `ignore = true` hoàn toàn bị tách khỏi phép tính đạo hàm ngược (Backpropagation).
    *   Khi tính điểm Benchmark (Recall/Precision), các Bbox này bị loại khỏi tập Ground Truth, bảo vệ mô hình không bị phạt oan.
*   **Giao thức Hoãn Phán Quyết (Abstain Protocol - Quyền Không Đoán Mò):**
    *   Khi một khung hình gặp tình huống mù quang học (mưa quá dày che mờ hoàn toàn, sương mù kết hợp chóa đèn pha), Annotator **tuyệt đối không được đoán mò** theo cảm tính cá nhân.
    *   Annotator kích hoạt nút **Abstain (Hoãn phán quyết)** trên CVAT, gắn nhãn tag `can_xem_lai` và chuyển khung hình vào hàng đợi **Escalation Queue**.
    *   Hội đồng phân xử (Adjudicator) sẽ sử dụng dữ liệu viễn thám bổ trợ từ **Radar sóng milimet 77GHz** và tín hiệu gia tốc xe để xác nhận xem tại tọa độ đó thực tế có vật cản vật lý hay không trước khi chốt nhãn cuối cùng.

---

### 4.6 Bộ 10 Ca Biên (Edge Cases) Đặc Thù Giao Thông Việt Nam & Decision Log

Mọi quy định phân xử tranh chấp phải được văn bản hóa vào **Sổ Tay Quyết Định Kỹ Thuật (Decision Log - `decision_log.md`)**, kèm ảnh dẫn chứng trước và sau để bảo đảm tính kế thừa:

1.  **Người đi bộ nấp sau cột điện/thùng rác, chỉ thò ra 1 chân:**  
    $\implies$ **Xử lý:** Gán Amodal Bbox bao trọn toàn bộ chiều cao ước tính của một người trưởng thành ($1.6 - 1.7\text{m}$) tính từ bàn chân lên phía trên, đánh cờ `occlusion_level = 50-80%`, `class_name = Pedestrian`.
2.  **Xe máy khuất sau đuôi xe tải, chỉ nhô ra gương chiếu hậu và tay lái:**  
    $\implies$ **Xử lý:** Nếu nhìn thấy tay lái + gương chiếu hậu: Vẽ Amodal Bbox kích thước xe máy tiêu chuẩn ($w \approx 0.8\text{m}, h \approx 1.2\text{m}$), cắm cờ `occlusion_level = 50-80%`. Nếu chỉ thấy một góc gương đơn độc không đủ cơ sở xác nhận: Vẽ Bbox quanh gương và đánh cờ `ignore = true`.
3.  **Người dắt bộ xe máy (Xe hỏng / hết xăng):**  
    $\implies$ **Xử lý:** **Gộp thành 1 Bbox duy nhất**. Nếu đang dắt ở sát lề đường hoặc vỉa hè $\implies$ Gán `class_name = Pedestrian`. Nếu đang dắt xe cắt ngang giữa lòng đường hoặc đi ngược chiều giữa làn xe chạy $\implies$ Gán `class_name = UNKNOWN_OBJECT` kèm cờ `motion_state = crossing` (vì khối thể tích này chiếm diện tích gấp 3 lần người đi bộ thông thường).
4.  **Xe máy chở kẹp 3, kẹp 4, trẻ nhỏ ngồi trước và sau:**  
    $\implies$ **Xử lý:** **CẤM TÁCH RIÊNG NGƯỜI VÀ XE**. Gán duy nhất 1 Bbox `Motorcycle` ôm trọn người lái, toàn bộ hành khách ngồi cùng, giỏ xe và thân xe. Đáy Bbox chạm điểm tiếp xúc mặt đường của 2 bánh xe.
5.  **Đèn pha xe tải ngược chiều chiếu lóa trắng bão hòa nửa khung hình:**  
    $\implies$ **Xử lý:** Vẽ một Bbox bao trùm toàn bộ quầng sáng bão hòa ($Pixel = 255$), gán `class_name = UNKNOWN_OBJECT`, đánh cờ `ignore = true`. Toàn bộ các bóng mờ bên trong quầng sáng này đều được bỏ qua tính loss.
6.  **Vệt nước mưa đọng trên kính chắn gió tạo ảo ảnh hình người (Ghosting Artifact):**  
    $\implies$ **Xử lý:** Annotator bắt buộc lùi timeline xem liên tiếp 3 khung hình kề trước và kề sau. Nếu vệt hình dạng không có chuyển động tịnh tiến quang học đồng bộ với mặt đường (vết ố đứng yên so với khung hình camera trong khi xe đang chạy) $\implies$ **CẤM GÁN BBOX** (đây là nhiễu quang học bề mặt kính).
7.  **Đám đông 3-5 người đi bộ chen chúc tại vạch sang đường:**  
    $\implies$ **Xử lý:** Nếu từng cá nhân nhìn thấy rõ $\ge 20\%$ cơ thể $\implies$ Vẽ từng Bbox `Pedestrian` riêng biệt cho từng người. Nếu dính chặt thành khối hỗn tạp không thể phân định chân/đầu của từng cá thể $\implies$ Vẽ 1 Bbox lớn bao quanh toàn bộ khối đám đông, gán `class_name = Pedestrian` và đánh cờ `ignore = true`.
8.  **Xe máy tạt đầu đột ngột (Cut-in) cự ly gần ($< 10\text{m}$), mới nhô 1/3 thân xe vào làn:**  
    $\implies$ **Xử lý:** **BẮT BUỘC vẽ Amodal Bbox** ước lượng trùm toàn bộ chiều dài xe máy ($1.8\text{m}$) dù phần lớn thân xe còn nằm ngoài mép ảnh/ngoài làn; đánh cờ `motion_state = cut_in`, `lane_relation = in_lane`, `distance_band = lt10m`. Đây là tình huống sống còn kích hoạt AEB!
9.  **Bóng người / bóng xe phản chiếu trên mặt đường ướt sũng hoặc tường kính showroom:**  
    $\implies$ **Xử lý:** **CẤM GÁN NHÃN VÀO BÓNG PHẢN CHIẾU**. Chỉ vẽ Bbox bao quanh cơ thể thực. Đáy Bbox kết thúc tại điểm chân thực tiếp xúc mặt đất, không kéo xuống vùng bóng phản chiếu.
10. **Hình người / xe máy in trên biển quảng cáo tấm lớn hoặc thùng xe buýt:**  
    $\implies$ **Xử lý:** **CẤM TUYỆT ĐỐI GÁN BBOX**. Annotator đối chiếu mặt phẳng chuyển động: hình ảnh chuyển động cùng tốc độ và nằm trên mặt phẳng vỏ xe buýt $\implies$ Bỏ qua hoàn toàn.

---

### 4.7 Bản Đặc Tả Triển Khai Kỹ Thuật Trên Nền Tảng CVAT (CVAT Machine-Readable Specs & Ops)

Để đưa các bản đặc tả nhãn vào vận hành thực tế mà không xảy ra lỗi gãy vỡ dữ liệu động học, Tổ C thiết lập cấu hình chuẩn trên nền tảng CVAT (Computer Vision Annotation Tool) bao gồm 4 cấu phần:

#### 1. Tử Huyệt Kỹ Thuật Trong CVAT: Cờ Biến Thiên Thời Gian (`mutable: true`)
Trong CVAT Track Mode, nếu một thuộc tính không được khai báo rõ `mutable: true`, CVAT sẽ mặc định xem thuộc tính đó là **bất biến xuyên suốt toàn bộ Track (Immutable/Track-level Attribute)**:
*   *Hậu quả chết người:* Khi xe máy ban đầu chạy ở lề đường (`lane_relation: near_lane`), đến frame thứ 20 tạt đầu vào làn xe VinFast (`lane_relation: in_lane`). Nếu thuộc tính để `mutable: false`, việc annotator chọn `in_lane` ở frame 20 sẽ **đè bẹp toàn bộ 19 frames trước đó thành `in_lane`**! Điều này làm sai lệch hoàn toàn nhãn động học huấn luyện mạng phát hiện va chạm sớm.
*   *Quy tắc Bất biến:* Toàn bộ 8 thuộc tính động học, không gian, rủi ro, che khuất và cờ kiểm soát (`occlusion_level`, `truncated`, `lane_relation`, `distance_band`, `motion_state`, `risk_status`, `ignore`, `prelabel_modified`) **BẮT BUỘC KHAI BÁO `<mutable>true</mutable>`** để lưu trữ giá trị độc lập trên từng Keyframe của Track!

#### 2. Bản Đặc Tả Định Dạng Chuẩn CVAT 2.x REST API (Raw JSON Specification)
Được nạp trực tiếp qua CVAT REST API v2 (`POST /api/tasks/{id}`) hoặc dán vào giao diện UI Raw Label Constructor:

```json
[
  {
    "name": "Pedestrian",
    "color": "#FF0000",
    "type": "rectangle",
    "attributes": [
      {"name": "occlusion_level", "input_type": "select", "mutable": true, "values": ["0-25%", "25-50%", "50-80%", ">80%"], "default_value": "0-25%"},
      {"name": "truncated", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "lane_relation", "input_type": "radio", "mutable": true, "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "out_of_lane"},
      {"name": "distance_band", "input_type": "radio", "mutable": true, "values": ["lt10m", "10to25m", "gt25m"], "default_value": "gt25m"},
      {"name": "motion_state", "input_type": "select", "mutable": true, "values": ["static", "along", "crossing", "cut_in"], "default_value": "static"},
      {"name": "risk_status", "input_type": "select", "mutable": true, "values": ["normal", "threatening", "colliding", "post_crash"], "default_value": "normal"},
      {"name": "ignore", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "prelabel_modified", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"}
    ]
  },
  {
    "name": "Motorcycle",
    "color": "#FFA500",
    "type": "rectangle",
    "attributes": [
      {"name": "occlusion_level", "input_type": "select", "mutable": true, "values": ["0-25%", "25-50%", "50-80%", ">80%"], "default_value": "0-25%"},
      {"name": "truncated", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "lane_relation", "input_type": "radio", "mutable": true, "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "near_lane"},
      {"name": "distance_band", "input_type": "radio", "mutable": true, "values": ["lt10m", "10to25m", "gt25m"], "default_value": "10to25m"},
      {"name": "motion_state", "input_type": "select", "mutable": true, "values": ["along", "cut_in", "crossing", "static"], "default_value": "along"},
      {"name": "risk_status", "input_type": "select", "mutable": true, "values": ["normal", "threatening", "colliding", "post_crash"], "default_value": "normal"},
      {"name": "ignore", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "prelabel_modified", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"}
    ]
  },
  {
    "name": "Bicycle",
    "color": "#FFFF00",
    "type": "rectangle",
    "attributes": [
      {"name": "occlusion_level", "input_type": "select", "mutable": true, "values": ["0-25%", "25-50%", "50-80%", ">80%"], "default_value": "0-25%"},
      {"name": "truncated", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "lane_relation", "input_type": "radio", "mutable": true, "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "near_lane"},
      {"name": "distance_band", "input_type": "radio", "mutable": true, "values": ["lt10m", "10to25m", "gt25m"], "default_value": "gt25m"},
      {"name": "motion_state", "input_type": "select", "mutable": true, "values": ["along", "crossing", "static", "cut_in"], "default_value": "along"},
      {"name": "risk_status", "input_type": "select", "mutable": true, "values": ["normal", "threatening", "colliding", "post_crash"], "default_value": "normal"},
      {"name": "ignore", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "prelabel_modified", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"}
    ]
  },
  {
    "name": "Car",
    "color": "#0000FF",
    "type": "rectangle",
    "attributes": [
      {"name": "occlusion_level", "input_type": "select", "mutable": true, "values": ["0-25%", "25-50%", "50-80%", ">80%"], "default_value": "0-25%"},
      {"name": "truncated", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "lane_relation", "input_type": "radio", "mutable": true, "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "in_lane"},
      {"name": "distance_band", "input_type": "radio", "mutable": true, "values": ["lt10m", "10to25m", "gt25m"], "default_value": "10to25m"},
      {"name": "motion_state", "input_type": "select", "mutable": true, "values": ["along", "static", "crossing", "cut_in"], "default_value": "along"},
      {"name": "risk_status", "input_type": "select", "mutable": true, "values": ["normal", "threatening", "colliding", "post_crash"], "default_value": "normal"},
      {"name": "ignore", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "prelabel_modified", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"}
    ]
  },
  {
    "name": "Bus_Truck",
    "color": "#800080",
    "type": "rectangle",
    "attributes": [
      {"name": "occlusion_level", "input_type": "select", "mutable": true, "values": ["0-25%", "25-50%", "50-80%", ">80%"], "default_value": "0-25%"},
      {"name": "truncated", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "lane_relation", "input_type": "radio", "mutable": true, "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "in_lane"},
      {"name": "distance_band", "input_type": "radio", "mutable": true, "values": ["lt10m", "10to25m", "gt25m"], "default_value": "gt25m"},
      {"name": "motion_state", "input_type": "select", "mutable": true, "values": ["along", "static", "cut_in", "crossing"], "default_value": "along"},
      {"name": "risk_status", "input_type": "select", "mutable": true, "values": ["normal", "threatening", "colliding", "post_crash"], "default_value": "normal"},
      {"name": "ignore", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "prelabel_modified", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"}
    ]
  },
  {
    "name": "UNKNOWN_OBJECT",
    "color": "#808080",
    "type": "rectangle",
    "attributes": [
      {"name": "occlusion_level", "input_type": "select", "mutable": true, "values": ["0-25%", "25-50%", "50-80%", ">80%"], "default_value": "0-25%"},
      {"name": "truncated", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "lane_relation", "input_type": "radio", "mutable": true, "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "in_lane"},
      {"name": "distance_band", "input_type": "radio", "mutable": true, "values": ["lt10m", "10to25m", "gt25m"], "default_value": "lt10m"},
      {"name": "motion_state", "input_type": "select", "mutable": true, "values": ["static", "crossing", "along", "cut_in"], "default_value": "static"},
      {"name": "risk_status", "input_type": "select", "mutable": true, "values": ["normal", "threatening", "colliding", "post_crash"], "default_value": "normal"},
      {"name": "ignore", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"},
      {"name": "prelabel_modified", "input_type": "checkbox", "mutable": true, "values": ["false"], "default_value": "false"}
    ]
  }
]
```

#### 3. Bảng Ánh Xạ Mô Hình AI Pre-label (Nuclio Serverless Function Mapping)
Khi tích hợp mô hình phát hiện candidate boxes (YOLOv10 / Grounding DINO) chạy dưới dạng Nuclio serverless function, hàm runtime tự động thực hiện ánh xạ từ nhãn COCO 80 lớp sang 6 lớp VinFast:

```python
# nuclio_function.py (Trích đoạn code ánh xạ nhãn tự động):
COCO_TO_VINFAST_SPEC = {
    0: "Pedestrian",      # COCO 'person' -> VinFast 'Pedestrian'
    1: "Bicycle",         # COCO 'bicycle' -> VinFast 'Bicycle' (kèm rider)
    2: "Car",             # COCO 'car' -> VinFast 'Car'
    3: "Motorcycle",      # COCO 'motorcycle' -> VinFast 'Motorcycle' (kèm rider)
    5: "Bus_Truck",       # COCO 'bus' -> VinFast 'Bus_Truck'
    7: "Bus_Truck",       # COCO 'truck' -> VinFast 'Bus_Truck'
}
# Lưu ý: Class UNKNOWN_OBJECT (đá tảng, lốp vỡ, trâu bò) CẤM tự động sinh bằng AI
# để tránh False Positives hàng loạt; bắt buộc do annotator xác thực thủ công.
```

#### 4. Bản Đồ Phím Tắt Công Thái Học (Ergonomics Hotkey Map) Cho Chuyên Viên Tổ C
Tối ưu hóa thao tác chuột và bàn phím giúp annotator thao tác đạt tốc độ $\ge 120\text{ box/giờ}$ mà không bị mỏi cơ:

| Thao tác (Action) | Phím tắt (Hotkey) | Hành vi Vận hành trong CVAT |
| :--- | :---: | :--- |
| **Chọn nhanh Lớp Nhãn** | Phím số `1` đến `6` | `1`: Pedestrian, `2`: Motorcycle, `3`: Bicycle, `4`: Car, `5`: Bus_Truck, `6`: UNKNOWN_OBJECT |
| **Vẽ Track Bbox Mới** | `Shift + N` | Tự động tạo Bbox ở chế độ Track Mode (không dùng Shape Mode) |
| **Chốt / Bật Keyframe** | `K` | Tạo Keyframe trên timeline để ghi nhận sự thay đổi tọa độ hoặc thuộc tính |
| **Bật / Tắt cờ `ignore`** | `I` | Đánh dấu vùng mù/quang học bão hòa hoặc Bbox $< 12\text{px}$ (Zero-loss) |
| **Lật cờ `risk_status`** | `Shift + R` | Chuyển nhanh trạng thái timeline: `normal` $\to$ `threatening` $\to$ `colliding` |
| **Tua Timeline Khung Hình** | `F` / `D` (1 frame)<br>`V` / `C` (10 frames) | Duyệt tiến/lùi dọc theo timeline hành trình xe để quan sát vector động học |

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

1.  **Frozen Clean Data Artifacts & SHA-256 Checksum:** Ảnh nén không mất dữ liệu kèm nhãn Parquet / COCO. Toàn bộ thư mục được cấp mã băm **SHA-256 Checksum** (chỉ cần thay đổi 1 pixel thì mã hash lập tức thay đổi) và bật chế độ **S3 Object Lock (WORM - Write Once, Read Many)** để chống can thiệp sửa đổi nhãn sau khi đã đóng băng.
2.  **Dataset Lineage & Version Control:** Hồ sơ nguồn gốc dữ liệu ghi vết bất biến: `dataset_version: v1.0.0`, `transform_pipeline_hash: git_commit_sha`, `annotation_guideline_version: v1.1` để đảm bảo truy ngược 100% nguồn gốc của mọi kết quả kiểm định.
3.  **Data Split Manifest (Chống Leakage làm mAP Ảo):** Tệp `split_manifest.json` chứng minh giao thoa tập hợp theo `trip_id` bằng chính xác $0.00\%$, ngăn chặn triệt để hiện tượng rò rỉ dữ liệu làm **mAP tăng cao giả tạo**.
4.  **Báo cáo Kiểm toán QC (Audit & Provenance Report):** Báo cáo nghiệm thu mẫu audit ngẫu nhiên 20% và Honeypot 5%, xác nhận tỷ lệ $\text{Miss Rate}_{\text{PII}} \le 0.001\%$ trên cả khuôn mặt và biển số xe ở mọi lát cắt môi trường.
5.  **Verification Test Suite & Dataset Card:** Bộ kiểm thử tự động xác nhận 0 tọa độ NaN, 0 box tràn viền, khớp hoàn hảo 5 chỉ số Loss-Check; kèm **Dataset Card (dataset_card.json)** công bố minh bạch ODD và Known Limitations (kính bám bùn $> 30\%$, tuyết dày).

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
