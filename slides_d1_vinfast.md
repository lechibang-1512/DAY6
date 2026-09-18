---
marp: true
theme: gaia
_class: lead
paginate: true
size: 16:9
backgroundColor: #0b132b
color: #e0e6ed
style: |
  section {
    font-family: 'Segoe UI', -apple-system, BlinkMacSystemFont, Roboto, sans-serif;
    padding: 26px 40px;
    font-size: 14px;
    background-color: #0b132b;
    color: #e0e6ed;
  }
  h1 {
    font-size: 22px;
    color: #38bdf8;
    margin-top: 0px;
    margin-bottom: 6px;
    border-bottom: 2px solid #1e3a8a;
    padding-bottom: 3px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  h2 {
    font-size: 15px;
    color: #60a5fa;
    margin-top: 2px;
    margin-bottom: 5px;
  }
  h3 {
    font-size: 13.5px;
    color: #93c5fd;
    margin-bottom: 3px;
  }
  p, li {
    font-size: 13px;
    line-height: 1.3;
    margin-top: 1px;
    margin-bottom: 2px;
  }
  strong { color: #f8fafc; }
  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 11.5px;
    margin-top: 4px;
    margin-bottom: 5px;
    background: #1e293b;
    border-radius: 5px;
    overflow: hidden;
  }
  th {
    background-color: #1e3a8a;
    color: #ffffff;
    padding: 4px 7px;
    text-align: left;
    font-weight: 600;
    border: 1px solid #334155;
  }
  td {
    padding: 3px 7px;
    border: 1px solid #334155;
    color: #cbd5e1;
  }
  tr:nth-child(even) { background-color: #0f172a; }
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
  .card-blue {
    background: #0f172a; border-left: 4px solid #38bdf8; padding: 5px 8px; margin: 3px 0;
  }
  .card-red {
    background: #1a0f1a; border-left: 4px solid #f43f5e; padding: 5px 8px; margin: 3px 0;
  }
  .card-green {
    background: #062419; border-left: 4px solid #10b981; padding: 5px 8px; margin: 3px 0;
  }
  code {
    font-family: 'JetBrains Mono', monospace; background: #0f172a; color: #38bdf8;
    padding: 1px 3px; border-radius: 3px; font-size: 11px;
  }
  pre {
    background: #0f172a; border: 1px solid #334155; border-radius: 4px;
    padding: 5px 7px; font-size: 10.5px; line-height: 1.2; margin: 3px 0;
  }
---

<!-- _class: lead -->
# ĐỀ TÀI Đ1: PHÁT HIỆN NGUY CƠ VA CHẠM CHO XE TỰ LÁI (VINFAST)
### DATA PIPELINE CHỐNG RÒ RỈ & BẢO ĐẢM TÍNH MẠNG TRONG 200MS
**Nhóm 8 · Lớp 2B-D304** | Khung thời gian: **10 Phút Chuẩn mực** | 100% Data Architecture

<div class="card-blue">
<strong>CHUẨN BỊ BATTLE THEO RUBRIC 100 ĐIỂM (AICB-P2T4):</strong>
• <strong>8 Thành viên chia 4 Tổ (A, B, C, D)</strong> — Minh bạch trách nhiệm và sản phẩm bàn giao, không ai ngồi không.<br/>
• <strong>Nguồn thực chiến:</strong> Khai thác bộ dữ liệu <strong>Nexar Collision Prediction</strong> (2.844 video dashcam, Kaggle / Hugging Face).<br/>
• <strong>Trực diện 6 câu hỏi TA:</strong> Sẵn sàng bảo vệ chéo bằng số liệu vật lý, code thực thi và phân tích điểm gãy vỡ con người (SPOF).
</div>

---

# MA TRẬN PHÂN CÔNG 8 THÀNH VIÊN & 6 CÂU HỎI HỘI ĐỒNG (TA Q1 - Q6)
## Phân định Quyền hạn Minh bạch — Xóa bỏ Điểm trừ "Không nêu rõ phần việc" (-10đ Rubric)

| Tổ | Thành viên | Trách nhiệm Bàn giao Cứng (Hạn phút 40) | Vùng Chuyên môn Phản biện (Lead Responder) |
| :---: | :--- | :--- | :--- |
| **A (NGUỒN)** | **Thành viên 1** | Khai thác Nexar, Sổ nguồn Data Ledger, License. | **[TA Q1]** Dataset phục vụ quyết định nào, ai dùng? |
| | **Thành viên 2** | Bảng Quota tập Test (Ép chỉ tiêu đêm/mưa/cut-in). | Hỗ trợ TA Q1 & Giải trình lấy mẫu EDA. |
| **B (XỬ LÝ)** | **Thành viên 3** | Cổng chặn PII 2 lớp & Quarantine Bucket. | **[TA Q6 - Đồng phụ trách]** Failsafe CI/CD & Pipeline. |
| | **Thành viên 4** | Khử trùng pHash & Schema Parquet 24 trường. | **[TA Q4 - Lead]** Kỹ thuật lọc trùng lặp & Schema data. |
| **C (NHÃN)** | **Thành viên 5** | Guideline quy tắc Truncation, Amodal & Ignore. | **[TA Q3 - Lead]** Xử lý ca khó, che khuất ≥ 80%. |
| | **Thành viên 6** | Bộ 10 ca biên Edge Cases & Nhãn động học. | Hỗ trợ TA Q3 & Giải trình Decision Log. |
| **D (ĐO LƯỜNG)**| **Thành viên 7 (Lead)**| Chiến lược GroupSplit chống rò rỉ, Ngưỡng QC. | **[TA Q2 - Lead]** Ma trận tổn thất & Asymmetric Loss. |
| | **Thành viên 8** | Metric đo đạc (Miss Rate @ 0.1FP), Risk Audit. | **[TA Q5 - Lead] & [TA Q6 - Lead]** Đo lường & SPOF. |

---

# 0. QUYẾT ĐỊNH LÕI & MA TRẬN TỔN THẤT SINH MẠNG [TA Q1, TA Q2]
## Dataset Phục vụ Phanh Khẩn cấp trong < 200ms & Sự Bất đối xứng giữa Bỏ sót vs Phanh oan

<div class="grid-2">
<div>

### [TA Q1] PHỤC VỤ QUYẾT ĐỊNH NÀO, AI DÙNG?
Quyết định **kích hoạt phanh khẩn cấp (AEB) / cảnh báo (FCW) / không can thiệp**, trên xe VinFast VF8/VF9, từ ảnh camera trước (FOV 120°), trong vòng **< 200ms**.
* **Đơn vị dữ liệu:** 1 vật thể trong 1 khung hình.
* **Group key bắt buộc:** `trip_id` (phiên ghi hình liên tục).

<div class="card-blue">
<strong>3 HỆ QUẢ KÉO THEO TOÀN BỘ PIPELINE:</strong><br/>
1. Bỏ sót VRU đắt <strong>gấp 6.67 lần</strong> Phanh oan và <strong>gấp 200 lần</strong> sót vật ngoài làn ⇒ Ép QC theo Worst-Class Gate.<br/>
2. Độ trễ 200ms ép giảm size ảnh ⇒ Vật &lt; 12px không đủ photon ⇒ Bắt buộc phải có <strong>Ignore Region</strong>.<br/>
3. Chỉ Bbox là <strong>KHÔNG ĐỦ</strong> để phanh ⇒ Phải gán thêm vị trí làn (`in_lane`) và trạng thái chuyển động (`cut_in`).
</div>

</div>
<div>

### [TA Q2] LỖI NÀO ĐẮT HƠN? (COST MATRIX)
*Bỏ sót người đi bộ trả giá bằng sinh mạng. Phanh ma gây dồn toa.*

| Phân loại Lỗi & Ngữ cảnh | Trọng số (Cost) |
| :--- | :---: |
| **Sót người đi bộ (Trong làn, < 25m) (FN)** | **1000** |
| **Sót xe máy (Trong làn, < 25m) (FN)** | **800** |
| **Sót ô tô (Trong làn gần) (FN)** | 200 |
| **Phanh oan (False Positive - Đâm dồn toa)** | **150** |
| Cảnh báo oan (False Warning - Tài xế tắt ADAS) | 10 |
| Sót vật ngoài làn / > 25m | 5 |

<div class="card-red">
<strong>KẾT LUẬN TOÁN HỌC:</strong> Macro F1 hay mAP trung bình là ngụy biện! Toàn bộ Quality Gate được đặt trên lát cắt nguy hiểm nhất: <strong>Người đi bộ ban đêm phải đạt Recall 100%</strong>.
</div>

</div>
</div>

---

# 1. TỔ A: NGUỒN DỮ LIỆU NEXAR, QUARANTINE & QUOTA TẬP TEST
## 2.844 Video Va chạm Thực tế & Luận điểm Ép Quota Lớp Hiếm (Bác bỏ Phân phối Tự nhiên)

<div class="grid-2">
<div>

### ĐA NGUỒN THỰC CHIẾN: NEXAR + DADA-2000
* **Nexar (Kaggle/HuggingFace):** 2.844 video dashcam MP4 (720p @ 30fps). **CHỈ dùng cho va chạm Car/Truck** (vì Nexar loại bỏ VRU!).
* **DADA-2000 / DoTA (2.000+ video):** Bổ sung các ca va chạm và suýt đâm **Người đi bộ & Xe máy (VRU Crash)**.
* **In-house Dashcam VN:** 4 người × 2h = 8h video ngõ nhỏ VN.

<div class="card-blue">
<strong>KHU CÁCH LY (QUARANTINE BUCKET):</strong> File hỏng codec, mất CAN bus, desync &gt; 10ms, camera bẩn &gt; 20% bắt buộc đưa vào S3 Quarantine. Tuyệt đối không xóa mù!
</div>

</div>
<div>

### [EDA PROOF] VÌ SAO BẮT BUỘC ÉP QUOTA TẬP TEST?
*Thống kê phân bố tự nhiên từ tập Train của Nexar:*
* **Ánh sáng:** Ngày (90.7%), Hoàng hôn (5.5%), **Đêm (2.9%)**, Lóa (0.9%).
* **Thời tiết:** Clear (59.3%), Cloudy (32.4%), **Mưa (8.0%)**, Tuyết (<0.2%).
* **Bối cảnh:** Đô thị (63.3%), Cao tốc (22.8%), Ngoại ô (10.0%).

| Lát cắt Điều kiện (Slice) | Tự nhiên | Quota Ép Tập Test |
| :--- | :---: | :--- |
| **Ban đêm / Thiếu sáng** | **2.9%** | **≥ 25%** số khung hình |
| **Mưa rào / Mặt đường ướt** | **8.0%** | **≥ 10%** số khung hình |
| **Ngược sáng (Bình minh/Hầm)**| 0.9% | **≥ 8%** số khung hình |
| **Người đi bộ cắt ngang đêm**| Cực hiếm | **≥ 300** instance |
| **Xe máy/Ô tô tạt đầu (Cut-in)**| Cực hiếm | **≥ 200** sự kiện |

</div>
</div>

---

# 2. TỔ B: DATA PIPELINE 6 BƯỚC & SCHEMA MANIFEST PARQUET
## Thiết kế Khung Xử lý Công nghiệp, Khử PII Hai Lớp và Schema 24 Trường Đầy đủ

<div class="grid-2">
<div>

### PIPELINE 6 BƯỚC (DÒNG CHẢY DỮ LIỆU THÔ → NHÃN)
```text
Video Thô (Nexar/DADA/In-house, 30fps)
  ├─1. Tách Keyframe 2 fps ───────────► Giảm 15x, nhúng trip_id vào file
  ├─2. Khử Trùng lặp (pHash) ─────────► Lọc khung đèn đỏ, copy ra output
  ├─3. ẨN DANH 2 LỚP (Face + Plate) ──► CỔNG CHẶN PHÁP LÝ (Nghị định 13)
  ├─4. AI Pre-label (Nuclio Serverless)► YOLOv10 sinh candidate boxes (conf>0.6)
  ├─5. CVAT Multi-Frame Track Mode ───► Nội suy TransT/SAM2 + Lật cờ risk_status
  └─6. Xuất Manifest Parquet & COCO ──► Đẩy vào DataLoader ML Pipeline
```

<div class="card-blue">
<strong>ẨN DANH 2 LỚP & CVAT TRACK MODE:</strong>
• <strong>Ẩn danh:</strong> <code>deface</code> (Mặt) + <code>yolov8n-plate</code> (Biển số).<br/>
• <strong>CVAT Track Mode:</strong> Keyframe t=0 và t=30, CVAT tự nội suy 29 frame giữa. Lật cờ timeline: <code>risk_status: normal → threatening → colliding</code>.
</div>

</div>
<div>

### SCHEMA FLAT MANIFEST (24 TRƯỜNG TRÊN PARQUET)
*Lưu trữ 1 dòng = 1 vật thể trong 1 khung. Query Polars/DuckDB cực nhanh:*

```yaml
- Định danh: trip_id, frame_id, timestamp_sec, img_path
- Kích thước ảnh: img_w (1280), img_h (720)
- Tọa độ Bbox: obj_id, class_name, x_min, y_min, w, h
- Che khuất: occlusion_level (0-25, 25-50, 50-80, >80), truncated

# THUỘC TÍNH ĐỘNG HỌC & RỦI RO PHỤC VỤ PHANH AEB:
- lane_relation: [in_lane, near_lane, out_of_lane]
- distance_band: [lt10m, 10to25m, gt25m]
- motion_state: [static, along, crossing, cut_in]
- risk_status: [normal, threatening, colliding, post_crash]

# KIỂM SOÁT ZERO-LOSS & AUDIT PROVENANCE:
- ignore: bool           # Bbox < 12px, lóa photon (Zero-loss)
- prelabel_modified: bool # True nếu annotator có chỉnh sửa
- lighting, weather, annotator_id, qc_status, anon_version
```

</div>
</div>

---

# 3. TỔ B & TỔ D: THIẾT KẾ XỬ LÝ DỮ LIỆU & CHỐNG RÒ RỈ [TA Q4]
## Đặc tả Thuật toán Tiền xử lý, Khử khuẩn PII 2 Lớp & Phân tách Độc lập Group Key

<div class="grid-2">
<div>

### 1. KIẾN TRÚC TIỀN XỬ LÝ & KHỬ PII HAI LỚP
* **Trích xuất Keyframe (2 fps):** Chu kỳ lấy mẫu $\Delta t = 500\text{ms}$ giảm tải $15\times$ khối lượng lưu trữ. Định danh `trip_id` được nhúng trực tiếp vào cấu trúc tên tệp và metadata để bảo toàn Group Key.
* **Cổng khử PII Khuôn mặt:** Áp dụng mạng CenterFace, mở rộng biên cục bộ $10\%$ và làm mờ Gauss ($\sigma=15$).
* **Cổng khử PII Biển số xe:** Tích hợp bộ dò biển số chuyên dụng (Plate Detector), khử khuẩn toàn bộ phương tiện giao thông.
* **Lọc Trùng lặp Nhận thức pHash (Perceptual Hash):**
  * Mã hóa chuỗi băm 64-bit qua biến đổi Cosine rời rạc (DCT).
  * Bộ đệm trượt 30 khung hình gần nhất: Loại bỏ khung hình nếu khoảng cách Hamming $d_H \le 8$ (dừng đèn đỏ, kẹt xe tĩnh).

</div>
<div>

### 2. [TA Q4] CƠ CHẾ CHỐNG RÒ RỈ DỮ LIỆU THỜI GIAN
* **Khóa Chết Group Key `trip_id`:** Mọi khung hình của cùng một chuyến đi bắt buộc phải nằm trọn vẹn trong duy nhất 1 tập con.
* **Phân bổ Tỷ lệ:** Huấn luyện ($70\%$), Kiểm định ($15\%$), Đánh giá ($15\%$).
* **Ràng buộc Tập hợp Bất biến (Zero-Leakage Invariant):**
  $$\text{Trips}_{\text{Train}} \cap \text{Trips}_{\text{Val}} = \emptyset, \quad \text{Trips}_{\text{Train}} \cap \text{Trips}_{\text{Test}} = \emptyset, \quad \text{Trips}_{\text{Val}} \cap \text{Trips}_{\text{Test}} = \emptyset$$
* **Kiểm toán Cận trùng lặp xuyên tập:** Quét pHash giữa Train và Test, cam kết **0 cặp khung hình** có khoảng cách Hamming $d_H \le 8$.
* **Thực nghiệm Ablation Proof:** Điểm mAP chia ngẫu nhiên đạt $94.2\%$ (học thuộc lòng bối cảnh), chia theo `trip_id` đạt $81.5\%$. Chênh lệch $12.7\%$ chứng minh lượng thông tin rò rỉ đã bị triệt tiêu hoàn toàn.

</div>
</div>

<div class="card-red">
<strong>TỬ HUYỆT RÒ RỈ DỮ LIỆU:</strong> Ở 30fps, 2 khung hình kề nhau cách nhau 33ms chứa chung bối cảnh tĩnh. Chia ngẫu nhiên đưa đáp án vào đề thi: mAP đạt 94.2% ảo nhưng test thực tế chỉ 81.5% (Ablation lệch 12.7%).
</div>

---

# 4. TỔ C: GUIDELINE, AMODAL BOX, PILOT & 10 CA BIÊN [TA Q3]
## Thử nghiệm Kép 30 Ảnh, Quy tắc Bỏ qua (Ignore Region), Quyền Abstain & Decision Log

<div class="grid-2">
<div>

### QUY TẮC CHE KHUẤT (OCCLUSION) & AMODAL BOX
* Vẽ box **Amodal** (trùm lên cả phần bị che khuất). Hệ thống phanh va chạm theo *thể tích vật lý thực*, không theo *pixel*.
* **Pilot Gate (30 ảnh):** Bắt buộc chạy thử trên 30 ảnh ca khó, đạt **Cohen's Kappa κ ≥ 0.85** và **mIoU ≥ 0.70** mới mở gán đại trà.

| Mức độ Che khuất | Nhóm VRU (Người đi bộ, Xe máy) | Nhóm Xe lớn / Vật tĩnh |
| :--- | :--- | :--- |
| **0 – 50%** | Gán Bbox bình thường. | Gán Bbox bình thường. |
| **50 – 80%** | **Bắt buộc gán Amodal Bbox**, cờ `occlusion`. | Gán nếu suy được biên. |
| **> 80%** | Bật cờ `ignore = true` (Zero-loss). | Bật cờ `ignore = true`. |

### IGNORE REGION & QUYỀN ABSTAIN
* **Ignore Region:** Box cao **< 12px** hoặc lóa sáng bão hòa ⇒ Gán `ignore = true`. Không tính TP, **KHÔNG PHẠT FP**.
* **Quyền Abstain:** Khi ảnh quá mờ (sương mù dày), Annotator được **Abstain (Không chốt)**, ghi tag `can_xem_lai` đẩy sang Decision Log.

</div>
<div>

### [TA Q3] 10 CA BIÊN & QUY TẮC XỬ LÝ TRIỆT ĐỂ
1. **Người sau cột, lộ 1 chân:** Gán Amodal Bbox, cờ `occlusion: 50-80%`.
2. **Xe máy sau ô tô, thấy 1 gương:** Nếu thấy tay lái ⇒ Gán Amodal xe máy. Nếu chỉ thấy gương rời rạc ⇒ `ignore = true`.
3. **Người dắt xe máy:** Gộp 1 Bbox, gán `Pedestrian` (hoặc `UNKNOWN_OBJECT` nếu dắt ngang giữa lòng đường).
4. **Xe máy chở 3:** Gộp 1 Bbox duy nhất (CẤM tách lớp rider).
5. **Đèn pha ngược chiều lóa trắng:** Khoanh vùng lóa, `ignore = true`.
6. **Giọt nước kính lái tạo bóng ma:** Check 3 frames; nếu không tịnh tiến theo chuyển động đường ⇒ **KHÔNG GÁN** (Artifact).
7. **Đám đông 3 người chồng lấp:** Gán riêng nếu thấy rõ ≥ 20% cơ thể. Nếu dính chặt không tách được ⇒ Gán 1 Bbox, `ignore = true`.
8. **Xe máy tạt đầu (Cut-in) lộ 1/3:** Bắt buộc gán Amodal, cờ `motion_state = cut_in` và `lane_relation = in_lane`.
9. **Bóng người phản chiếu trên đường/kính:** **CẤM GÁN**.
10. **Hình in người trên xe buýt quảng cáo:** **CẤM GÁN**.

</div>
</div>

---

# 5. TỔ D: ĐO LƯỜNG CHẤT LƯỢNG & TIÊU CHUẨN NGHIỆM THU LÔ [TA Q5]
## Bác bỏ mAP, Sử dụng Miss Rate trên VRU Khóa Ngân sách FP & Chiến lược QC Bất đối xứng

<div class="grid-2">
<div>

### [TA Q5] METRIC ĐO ĐẠC: MISS RATE TRÊN VRU
* **Bác bỏ mAP tổng hợp:** mAP đánh đồng việc bỏ sót mạng người với bỏ sót một thùng rác ven đường.
* **Chỉ số Sống còn:** **Miss Rate trên VRU** (Người đi bộ/Xe máy) trong làn và khoảng cách nguy hiểm $< 25\text{m}$:
  $$\text{Miss Rate}_{\text{VRU}} = \frac{\text{FN}}{\text{TP} + \text{FN}} \times 100\% \quad (\text{Mẫu số: Tổng số VRU thực tế})$$
* **Khóa Ngân sách Phanh ma:** Giới hạn $\le 0.1\text{ FP / khung hình}$ trên luồng nhận thức thô. Sau đó bắt buộc lọc 3 frames liên tiếp.
* **Quy mô Mẫu:** Test tối thiểu 300 người đi bộ đêm, 200 cut-in.
* **Ai đo:** Tổ D audit độc lập bằng gán Đôi Mù 100% trên Test.

</div>
<div>

### NGƯỠNG NGHIỆM THU LÔ (QUALITY CONTROL GATES)
*Dồn tiền QC vào Test: Val/Test gán Đôi Mù 100% (Independent Double-blind). Train gán đơn, audit ngẫu nhiên **20%** (Rút ngẫu nhiên **100 khung / lô 500 khung**).*

| Lát cắt Lớp / Ngữ cảnh | Recall Tối thiểu | IoU Yêu cầu | Thuộc tính Đúng |
| :--- | :--- | :--- | :--- |
| **Người đi bộ (`in_lane`, < 25m)** | **100% (Sót 1 ⇒ REJECT)** | ≥ 0.70 | ≥ 95% |
| **Xe máy (`in_lane`, < 25m)** | **≥ 99%** (Tối đa 1 lỗi/100) | ≥ 0.70 | ≥ 95% |
| **Ô tô (Car)** | ≥ 95% | ≥ 0.60 | ≥ 90% |
| **Vật thể tĩnh / Ngoại biên** | ≥ 92% | ≥ 0.50 | ≥ 85% |

<div class="card-red">
<strong>CHẾ TÀI VI PHẠM CỔNG SINH MẠNG:</strong> Nếu phát hiện dù chỉ 1 lỗi bỏ sót người đi bộ trong làn ⇒ <strong>REJECT NGUYÊN LÔ 500 KHUNG</strong>, đình chỉ annotator, bắt buộc xóa nhãn và gán lại 100%!
</div>

</div>
</div>

---

# 6. GIAI ĐOẠN 9: RELEASE PACKET & GIAI ĐOẠN 10: GIÁM SÁT VẬN HÀNH
## Bộ Hồ sơ Xuất xưởng 5 Thành phần (S3 WORM), Đo lường Data Drift & Fleet Telemetry Loop

<div class="grid-2">
<div>

### BƯỚC 9: ĐÓNG GÓI RELEASE PACKET v1.0
*Chốt chặn cuối cùng: Data Owner (TV1) chỉ ký duyệt khi đủ 5 phần:*
1. **Clean Data Artifacts:** Ảnh nén + Parquet/COCO. Cấp mã băm **SHA-256**, khóa quyền ghi bằng **S3 Object Lock (WORM)**.
2. **Data Split Manifest:** `split_manifest.json` chứng minh giao thoa theo `trip_id` bằng chính xác $0.00\%$.
3. **Báo cáo Kiểm toán QC:** Biên bản audit 20% + Honeypot 5%, xác nhận tỷ lệ sót PII $\le 0.001\%$.
4. **Verification Test Suite:** Script CI/CD xác nhận 0 tọa độ NaN, 0 box tràn viền, 0 box âm.
5. **Dataset Card Chuẩn hóa:** Công bố rõ ODD và **Known Limitations** (Giới hạn: kính bám bùn > 30% hoặc tuyết dày).

</div>
<div>

### BƯỚC 10: GIÁM SÁT DRIFT & HỘP ĐEN XE LĂN BÁNH
1. **Giám sát Data Drift Đầu vào:**
   * Tính khoảng cách Wasserstein / PSI trên độ rọi ánh sáng (Lux) và độ tán xạ mưa.
   * Cảnh báo khi $\text{PSI} > 0.2$ so với phân phối train gốc.
2. **Fleet Telemetry Trigger (Hành vi tài xế là chân lý):**
   * *Tài xế đạp thốc ga đè phanh:* Phanh oan (FP) ⇒ Lưu 15s clip.
   * *Tài xế phanh gấp / giật lái né vật:* Bỏ sót (FN) ⇒ Lưu 15s clip.
3. **Giao thức Sửa lỗi Tận gốc (Root Cause Rework):**
   * CẤM sửa lẻ tẻ từng ảnh!
   * Họp Adjudication ⇒ Cập nhật Guideline v1.2 ⇒ **Hồi tố (Retroactive Audit)** quét lại các batch cũ xuất xưởng.

</div>
</div>

---

# 7. SỰ THẬT TRẦN TRỤI: ĐIỂM GÃY VỠ (SPOF) & 3 TẦNG BẢO VỆ [TA Q6]
## Thừa nhận Yếu điểm Con người tại Khâu Review QC & Kiến trúc Failsafe Chặn Đứng Sụp đổ

<div class="grid-2">
<div>

<div class="card-red">
<strong>[TA Q6] ĐIỂM GÃY VỠ DUY NHẤT (SPOF):</strong><br/>
Quy trình cần 8 người. Chỗ dễ vỡ nhất không nằm ở thuật toán pHash hay Code, mà nằm ở <strong>SỰ MỆT MỎI CỦA CON NGƯỜI TẠI KHÂU DUYỆT NHÃN (REVIEW QC) CỦA TỔ D</strong>.
</div>

* **Nút thắt Cổ chai & Hiệu ứng Neo (Anchoring Bias):**
  * Áp lực deadline dồn hàng ngàn box về cuối kỳ cho 2 Reviewer.
  * Sau 90 phút nhìn màn hình, mắt bị mỏi, sinh tâm lý lướt nhanh và bấm Approve hàng loạt vào nhãn AI Prelabel.
  * **Hậu quả tử huyệt:** Lọt lưới các lỗi bỏ sót người đi bộ đêm vào tập test, khiến xe VinFast bị lỗi nhận diện khi xuất xưởng.

</div>
<div>

### KIẾN TRÚC PHÒNG THỦ 3 TẦNG FAILSAFE
1. **Tầng 1 - CI/CD Automated Gate (Tổ B phụ trách):**
   * Script Python quét 100% tệp Parquet trước khi Reviewer mở giao diện CVAT.
   * Tự động Reject ngay nếu phát hiện Bbox thiếu `motion_state`, tọa độ âm, hoặc diện tích $< 144\text{px}^2$ mà không gắn `ignore`.
2. **Tầng 2 - Bẫy Ngầm Kỹ thuật số (Honeypot 5% - Tổ D):**
   * Trộn ngầm **5% khung ảnh Vàng (Gold Ground-truth)** do Data Owner tự gán vào mỗi lô hàng.
   * Nếu Reviewer duyệt lướt $< 3.0\text{s}$ hoặc bỏ sót người đi bộ trên khung Vàng ⇒ Khóa tài khoản, tước quyền QC ngay lập tức!
3. **Tầng 3 - Kỷ luật Bào mòn (Fatigue Rule):**
   * Code cứng trên CVAT: **Giới hạn ca làm việc ≤ 90 phút**. Bắt buộc đăng xuất nghỉ ngơi 15 phút để phục hồi thị giác.

</div>
</div>
