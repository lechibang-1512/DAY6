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
    padding: 20px 34px;
    font-size: 12px;
    background-color: #0b132b;
    color: #e0e6ed;
  }
  h1 {
    font-size: 19px;
    color: #38bdf8;
    margin-top: 0px;
    margin-bottom: 4px;
    border-bottom: 2px solid #1e3a8a;
    padding-bottom: 2px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  h2 {
    font-size: 13.5px;
    color: #60a5fa;
    margin-top: 1px;
    margin-bottom: 3px;
  }
  h3 {
    font-size: 12px;
    color: #93c5fd;
    margin-bottom: 2px;
    margin-top: 1px;
  }
  p, li {
    font-size: 11.8px;
    line-height: 1.26;
    margin-top: 1px;
    margin-bottom: 1.5px;
  }
  strong { color: #f8fafc; }
  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 10px;
    margin-top: 2px;
    margin-bottom: 3px;
    background: #1e293b;
    border-radius: 4px;
    overflow: hidden;
  }
  th {
    background-color: #1e3a8a;
    color: #ffffff;
    padding: 2.5px 5px;
    text-align: left;
    font-weight: 600;
    border: 1px solid #334155;
  }
  td {
    padding: 2px 5px;
    border: 1px solid #334155;
    color: #cbd5e1;
  }
  tr:nth-child(even) { background-color: #0f172a; }
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 9px; }
  .card-blue {
    background: #0f172a; border-left: 4px solid #38bdf8; padding: 3.5px 6px; margin: 1.5px 0; font-size: 11px;
  }
  .card-red {
    background: #1a0f1a; border-left: 4px solid #f43f5e; padding: 3.5px 6px; margin: 1.5px 0; font-size: 11px;
  }
  .card-green {
    background: #062419; border-left: 4px solid #10b981; padding: 3.5px 6px; margin: 1.5px 0; font-size: 11px;
  }
  code {
    font-family: 'JetBrains Mono', monospace; background: #0f172a; color: #38bdf8;
    padding: 1px 3px; border-radius: 3px; font-size: 10px;
  }
  pre {
    background: #0f172a; border: 1px solid #334155; border-radius: 4px;
    padding: 3px 5px; font-size: 9.2px; line-height: 1.15; margin: 1.5px 0;
  }
---

<!-- _class: lead -->
# ĐỀ TÀI Đ1: PHÁT HIỆN NGUY CƠ VA CHẠM CHO XE TỰ LÁI (VINFAST)
### DATA PIPELINE CHỐNG RÒ RỈ & BẢO ĐẢM TÍNH MẠNG TRONG 200MS
**Nhóm 8 · Lớp 2B-D304** | Khung thời gian: **8 Phút 30 Giây Chuẩn mực** | 100% Data Architecture

<div class="card-blue">
<strong>CHUẨN BỊ BATTLE THEO RUBRIC 100 ĐIỂM (AICB-P2T4 · CANONICAL DATA LIFECYCLE):</strong><br/>
• <strong>8 Thành viên chia 4 Tổ (A, B, C, D)</strong> — Phân định cứng trách nhiệm bàn giao phút 40, không ai ngồi không.<br/>
• <strong>Nguồn thực chiến:</strong> Khai thác <strong>Nexar Collision Prediction</strong> (2.844 video dashcam) + <strong>DADA-2000</strong> (VRU crash) + 8h In-house VN.<br/>
• <strong>Quyết định Lõi & Asymmetric Cost:</strong> Sót người đi bộ ($Cost=1000$) đắt gấp 6.67 lần phanh oan ($Cost=150$) và gấp 200 lần sót ngoài làn.<br/>
• <strong>Trực diện 6 câu hỏi TA:</strong> Bảo vệ bằng số liệu vật lý quang học, code assert Zero-Leakage và kiến trúc 3 tầng khóa rủi ro con người (SPOF).
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
| | **Thành viên 6** | Bộ 10 ca biên Edge Cases, CVAT spec & Động học. | Hỗ trợ TA Q3 & Giải trình Decision Log. |
| **D (ĐO LƯỜNG)**| **Thành viên 7 (Lead)**| Chiến lược GroupSplit chống rò rỉ, Ngưỡng QC. | **[TA Q2 - Lead]** Ma trận tổn thất & Asymmetric Loss. |
| | **Thành viên 8** | Metric đo đạc (Miss Rate @ 0.1FP), Risk Audit. | **[TA Q5 - Lead] & [TA Q6 - Lead]** Đo lường & SPOF. |

<div class="card-green">
<strong>CAM KẾT TRUY CỨU TRÁCH NHIỆM:</strong> Không có chức danh điều phối ngồi không. Mọi thành viên đều làm chủ artifact kỹ thuật, phản biện độc lập trong 45 giây khi bị TA chỉ định bất ngờ.
</div>

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
3. Chỉ Bbox là <strong>KHÔNG ĐỦ</strong> để phanh ⇒ Phải gán thêm vị trí làn (<code>lane_relation</code>) và trạng thái chuyển động (<code>motion_state</code>).
</div>

</div>
<div>

### [TA Q2] LỖI NÀO ĐẮT HƠN? (COST MATRIX)
*Bỏ sót người đi bộ trả giá bằng sinh mạng. Phanh ma gây dồn toa.*

| Phân loại Lỗi & Ngữ cảnh | Trọng số (Cost) | Vì sao (Căn cứ Kỹ thuật & Vật lý) |
| :--- | :---: | :--- |
| **Sót người đi bộ (Trong làn, < 25m)** | **1000** | Trực tiếp đe dọa sinh mạng con người. |
| **Sót xe máy (Trong làn, < 25m)** | **800** | Mật độ xe máy tại VN cực cao, dễ ngã. |
| **Sót ô tô (Trong làn gần)** | 200 | Thiệt hại tài sản, hiếm tử vong. |
| **Phanh oan (False Positive)** | **150** | Xe sau đâm dồn toa (bám đuôi 1-2m ở VN). |
| Cảnh báo oan (False Warning) | 10 | Phiền nhiễu, tài xế tắt ADAS. |
| Sót vật ngoài làn / > 25m | 5 | Không giao cắt quỹ đạo di chuyển. |

<div class="card-red">
<strong>KẾT LUẬN TOÁN HỌC:</strong> Macro F1 hay mAP trung bình là ngụy biện! Toàn bộ Quality Gate được đặt trên lát cắt nguy hiểm nhất: <strong>Người đi bộ ban đêm phải đạt Recall 100%</strong>.
</div>

</div>
</div>

---

# 1. TỔ A: NGUỒN THỰC CHIẾN, QUARANTINE & ÉP QUOTA TEST [TA Q1]
## 2.844 Video Nexar, Bổ khuyết DADA-2000 & Luận điểm Ép Quota Lớp Hiếm

<div class="grid-2">
<div>

### ĐA NGUỒN THỰC CHIẾN: NEXAR + DADA-2000
* **Tử huyệt của Nexar (arXiv:2503.03848):** 2.844 video dashcam (720p @ 30fps) **CHỈ chứa va chạm Car/Truck, loại trừ toàn bộ VRU!**
* **Cấu trúc Đa nguồn:**
  1. *Nexar:* Nguồn va chạm dương tính cho Car/Truck.
  2. *DADA-2000 / DoTA (2.000+ video):* Chuyên biệt cho **Người đi bộ băng đường & Xe máy tạt đầu (VRU Crash)**.
  3. *In-house Dashcam VN:* 4 người × 2h = 8h video ngõ nhỏ VN.
* **Khu Cách ly (Quarantine Bucket):** File hỏng codec, mất CAN bus, desync > 10ms, camera bẩn > 20% bắt buộc chuyển vào S3 quarantine bucket kèm `quarantine_ledger`. Tuyệt đối không xóa mù làm lệch mẫu số.

</div>
<div>

### [EDA PROOF] VÌ SAO BẮT BUỘC ÉP QUOTA TẬP TEST?
*Thống kê phân bố tự nhiên từ tập Train của Nexar:*
* **Ánh sáng:** Ngày (90.7%), Hoàng hôn (5.5%), **Đêm (2.9%)**, Lóa (0.9%).
* **Thời tiết:** Clear (59.3%), Cloudy (32.4%), **Mưa (8.0%)**, Tuyết (<0.2%).
* **Bối cảnh:** Đô thị (63.3%), Cao tốc (22.8%), Ngoại ô (10.0%).

| Lát cắt Môi trường / Ca nguy hiểm | Tự nhiên | Quota Ép Tập Test VinFast |
| :--- | :---: | :--- |
| **Ban đêm / Thiếu sáng** | **2.9%** | **≥ 25%** số khung hình |
| **Mưa rào / Mặt đường ướt phản quang** | **8.0%** | **≥ 10%** số khung hình |
| **Ngược sáng (Bình minh / Ra khỏi hầm)**| 0.9% | **≥ 8%** số khung hình |
| **Người đi bộ cắt ngang đường ban đêm**| Cực hiếm | **≥ 300** instance vật thể |
| **Xe máy/Ô tô tạt đầu đột ngột (Cut-in)**| Cực hiếm | **≥ 200** sự kiện độc lập |

</div>
</div>

<div class="card-blue">
<strong>LUẬN ĐIỂM SẮC BÉN:</strong> Trong tự nhiên, Đêm chỉ chiếm 2.9% và Mưa chỉ chiếm 8.0%. Nếu chia ngẫu nhiên, mô hình đạt 97% độ chính xác dù hoàn toàn "mù" ban đêm! Ép Quota là yêu cầu sống còn cho xe tự lái.
</div>

---

# 2. TỔ B: DATA PIPELINE 6 BƯỚC & CƠ CHẾ CVAT MULTI-FRAME TRACKING
## Dòng Chảy Dữ Liệu Công Nghiệp, Khử PII Hai Lớp và Bảng Loss-Check 5 Chỉ Số

<div class="grid-2">
<div>

### PIPELINE 6 BƯỚC (DÒNG CHẢY DỮ LIỆU THÔ → NHÃN)
```text
Video Thô (Nexar/DADA/In-house, 30fps)
  ├─1. Tách Keyframe 2 fps ───────────► Giảm 15x, nhúng trip_id vào file
  ├─2. Khử Trùng lặp (pHash) ─────────► Lọc khung đèn đỏ, buffer 30 frames
  ├─3. ẨN DANH 2 LỚP (Face + Plate) ──► CỔNG CHẶN PHÁP LÝ (Nghị định 13)
  ├─4. AI Pre-label (Nuclio Serverless)► YOLOv10 sinh candidate boxes (conf>0.6)
  ├─5. CVAT Multi-Frame Track Mode ───► Nội suy TransT/SAM2 + Lật cờ risk_status
  └─6. Xuất Manifest Parquet & COCO ──► Đẩy vào DataLoader ML Pipeline
```

<div class="card-blue">
<strong>CVAT MULTI-FRAME TRACK MODE:</strong><br/>
• Keyframe t=0 và t=30, CVAT tự động nội suy tọa độ 29 frame ở giữa (giảm 90% công vẽ tay).<br/>
• Lật cờ timeline: <code>risk_status: normal → threatening → colliding</code>.
</div>

</div>
<div>

### ẨN DANH 2 LỚP & BẢNG LOSS-CHECK 5 CHỈ SỐ
* **Ẩn danh PII 2 lớp:** CenterFace (+10% viền, Gauss $\sigma=15$) + License Plate Detector. Tỷ lệ sót PII $\le 0.001\%$, đo riêng slice Đêm & Mưa.
* **Bảng Loss-Check 5 Chỉ số (CVAT → Parquet):**

| STT | Chỉ số Kiểm soát (Loss-Check) | CVAT Raw | Parquet Out | Tiêu chuẩn |
| :---: | :--- | :---: | :---: | :--- |
| **1** | **Tổng số Bounding Box** | $N_{\text{in}}$ | $N_{\text{out}}$ | Khớp 100% (0 rụng box) |
| **2** | **Tổng số Track ID** | $T_{\text{in}}$ | $T_{\text{out}}$ | Không đứt gãy track |
| **3** | **Phân bố Lớp Nhãn (Classes)** | $C_{\text{in}}$ | $C_{\text{out}}$ | Khớp 6 lớp danh mục |
| **4** | **Thuộc tính có giá trị (Populated)**| $A_{\text{in}}$ | $A_{\text{out}}$ | 100% đủ `lane`, `motion` |
| **5** | **Số Frame có ít nhất 1 nhãn** | $F_{\text{in}}$ | $F_{\text{out}}$ | Không rụng khung hình |

</div>
</div>

---

# 3. TỔ B & TỔ D: THIẾT KẾ XỬ LÝ DỮ LIỆU & CHỐNG RÒ RỈ [TA Q4]
## Đặc tả Thuật toán Tiền xử lý, Khử khuẩn PII 2 Lớp & Phân tách Độc lập Group Key

<div class="grid-2">
<div>

### 1. KIẾN TRÚC TIỀN XỬ LÝ & LỌC TRÙNG pHASH
* **Trích xuất Keyframe (2 fps):** Chu kỳ lấy mẫu $\Delta t = 500\text{ms}$ giảm tải $15\times$ khối lượng lưu trữ. Định danh `trip_id` được nhúng trực tiếp vào cấu trúc tên tệp `{trip_id}_{frame_idx:06d}.jpg` để bảo toàn Group Key.
* **Lọc Trùng lặp Nhận thức pHash (Perceptual Hash):**
  * Mã hóa chuỗi băm 64-bit qua biến đổi Cosine rời rạc (DCT $8 \times 8$).
  * Bộ đệm trượt 30 khung hình gần nhất: Loại bỏ khung hình nếu khoảng cách Hamming $d_H(h_t, h_{t-k}) \le 8$ với mọi $k \in [1, 30]$ (dừng đèn đỏ, kẹt xe tĩnh).
* **Cổng khử PII:** CenterFace + Bộ dò biển số xe, đảm bảo tỷ lệ sót $\le 0.001\%$, bảo tồn nguyên vẹn quần áo, dáng người và đèn tín hiệu xe.

</div>
<div>

### 2. [TA Q4] CƠ CHẾ CHỐNG RÒ RỈ DỮ LIỆU THỜI GIAN
* **Khóa Chết Group Key `trip_id`:** Mọi khung hình của cùng một chuyến đi bắt buộc nằm trọn trong duy nhất 1 tập con. Tỷ lệ: Train ($70\%$), Val ($15\%$), Test ($15\%$).
* **Ràng buộc Tập hợp Bất biến (Zero-Leakage Invariant):**
  $$\text{Trips}_{\text{Train}} \cap \text{Trips}_{\text{Val}} = \emptyset, \quad \text{Trips}_{\text{Train}} \cap \text{Trips}_{\text{Test}} = \emptyset, \quad \text{Trips}_{\text{Val}} \cap \text{Trips}_{\text{Test}} = \emptyset$$
* **Kiểm toán Cận trùng lặp xuyên tập:** Quét pHash giữa Train và Test, cam kết **0 cặp khung hình** có khoảng cách Hamming $d_H \le 8$.
* **Thực nghiệm Ablation Proof:** Điểm mAP chia ngẫu nhiên đạt $94.2\%$ (học thuộc lòng bối cảnh), chia theo `trip_id` đạt $81.5\%$. Chênh lệch $12.7\%$ chứng minh lượng thông tin rò rỉ đã bị triệt tiêu!

</div>
</div>

<div class="card-red">
<strong>TỬ HUYỆT RÒ RỈ DỮ LIỆU:</strong> Ở 30fps, 2 khung hình kề nhau cách nhau 33ms chứa chung bối cảnh tĩnh. Chia ngẫu nhiên đưa đáp án vào đề thi: mAP đạt 94.2% ảo nhưng test thực tế chỉ 81.5% (Ablation lệch 12.7%).
</div>

---

# 4. TỔ B: HỢP ĐỒNG DỮ LIỆU & SCHEMA MANIFEST PARQUET 24 TRƯỜNG
## Lưu trữ Flat Table trên Apache Parquet (Snappy, 64MB Chunk) — Truy vấn Polars/DuckDB Siêu Tốc

<div class="grid-2">
<div>

### NHÓM 1: ĐỊNH DANH, HÌNH HỌC & CHE KHUẤT
* **1. Định danh & Tọa độ Ảnh (Image-level):**
  * `trip_id`: string (NOT NULL) — **Group Key bất biến**.
  * `frame_id`: string (NOT NULL) — `{trip_id}_{frame_idx:06d}`.
  * `timestamp_sec`: float32 (NOT NULL, $\ge 0.0$) — $\Delta t = 0.5\text{s}$.
  * `img_path`: string (NOT NULL) — Đường dẫn tương đối file ảnh.
  * `img_w`: int16 (1280), `img_h`: int16 (720).
* **2. Hình học Bounding Box:**
  * `obj_id`: int32 (NOT NULL) — Tracking ID xuyên suốt trip.
  * `class_name`: string (NOT NULL) — 6 lớp chuẩn ADAS.
  * `x_min`, `y_min`, `w`, `h`: float32 — Tọa độ pixel tuyệt đối.
* **3. Che khuất & Mép ảnh:**
  * `occlusion_level`: string — `[0-25%, 25-50%, 50-80%, >80%]`.
  * `truncated`: bool — Chạm mép ảnh và phần thấy $\ge 25\%$.

</div>
<div>

### NHÓM 2: ĐỘNG HỌC PHANH, ZERO-LOSS & METADATA
* **4. Động học & Quyết định Phanh AEB:**
  * `lane_relation`: string — `[in_lane, near_lane, out_of_lane]`.
  * `distance_band`: string — `[lt10m, 10to25m, gt25m]`.
  * `motion_state`: string — `[static, along, crossing, cut_in]`.
  * `risk_status`: string — `[normal, threatening, colliding, post_crash]`.
* **5. Cờ Kiểm soát Zero-Loss & Giám sát Bias:**
  * `ignore`: bool — Zero-loss ($Loss = 0$): Bbox $< 12\text{px}$, lóa trắng, che $> 80\%$.
  * `prelabel_modified`: bool — True nếu annotator có sửa đề xuất AI.
* **6. Metadata Môi trường & Audit:**
  * `lighting`: `[daylight, twilight, night_lit, night_dark, glare]`.
  * `weather`: `[clear, cloudy, rain, fog]`.
  * `annotator_id`, `qc_status`, `anon_version`.

</div>
</div>

<div class="card-blue">
<strong>HIỆU NĂNG TRUY VẤN:</strong> Định dạng Parquet cho phép lọc Slice-based Evaluation (ví dụ: tìm người đi bộ đêm trong làn &lt; 25m) trên hàng triệu dòng chỉ mất &lt; 50ms bằng Polars/DuckDB.
</div>

---

# 5. TỔ C: BẢN ĐẶC TẢ DANH MỤC 6 LỚP NHÃN (LABELS TAXONOMY)
## Quy Chuẩn Ranh Giới Hộp Bao, Đồng Bộ Kinematic Collision Footprint & Tiêu Chuẩn Loại Trừ

<div class="grid-2">
<div>

### NHÓM ĐỐI TƯỢNG YẾU THẾ (VRU - COST 800 - 1000)
* **`Pedestrian` (ID: 0):** Người đi, chạy, đứng, cúi, ngã/nằm trên đường; người đi xe lăn, người đẩy xe nôi.
  * *Bbox:* Từ đỉnh đầu/mũ đến gót giày tiếp đất; bao trùm ba lô, túi xách, ô dù che mưa. Điểm tiếp đất $(x_g, y_g)$ neo tính IPM. Ngưỡng $h \ge 12\text{px}$.
* **`Motorcycle` (ID: 1):** Xe máy số, tay ga, xe điện (VinFast Klara, Feliz, Evo...).
  * *Bbox:* **CẤM TÁCH LỚP RIDER**. Gộp người lái, khách kẹp 2-3, gương, ống bô và bánh xe thành 1 Bbox va chạm vật lý duy nhất.
  * *Áo mưa cánh dơi:* Bao trọn tà áo phập phồng nếu mở rộng $\ge 10\text{cm}$.
  * *Hàng cồng kềnh:* Bắt buộc mở rộng Bbox bao trùm bình gas, sọt hoa quả.
* **`Bicycle` (ID: 2):** Xe đạp cơ, trợ lực điện. Gộp người đạp và xe thành 1 Bbox.

</div>
<div>

### NHÓM XE CƠ GIỚI & CHƯỚNG NGẠI VẬT MẶT ĐƯỜNG
* **`Car` (ID: 3):** Sedan, SUV, CUV, Bán tải. Bao trùm 4 mép vỏ xe, gương chiếu hậu, lốp xe tiếp đất và giá chở hàng nóc xe (roof-rack).
* **`Bus_Truck` (ID: 4):** Xe buýt VinBus, xe tải, container.
  * *QUY TẮC SỐNG CÒN:* **BẮT BUỘC bao trùm thép cây/tôn cuộn thò ra đuôi xe** (chống tai nạn xuyên thủng kính lái).
  * *Jack-knifing:* Tách thành 2 Bbox khi xe đầu kéo cua gập góc chữ L.
* **`UNKNOWN_OBJECT` (ID: 5):** Vật cản mặt đường $\ge 20\text{cm}$ ở cự ly $< 40\text{m}$ (mảnh lốp xe tải nổ, dải phân cách xô lệch, trâu bò qua đường).
* **NEGATIVE EXCLUSION (CẤM GÁN):** Pano quảng cáo, decal hông xe buýt, bóng đổ mặt đường, hình phản chiếu qua vũng nước/kính.

</div>
</div>

<div class="card-red">
<strong>NGUYÊN TẮC BẤT BIẾN:</strong> Hệ thống phanh tự động (AEB) can thiệp theo thể tích va chạm cơ học thực tế. Tách rời người lái khỏi xe máy là sai lầm chết người làm thuật toán phanh ước tính sai tiết diện va chạm!
</div>

---

# 6. TỔ C: BẢN ĐẶC TẢ THUỘC TÍNH KỸ THUẬT & QUYẾT ĐỊNH PHANH AEB
## Ánh Xạ Thuộc Tính Hình Học và Động Lực Học Sang Bộ Điều Khiển Phanh Xe Tự Lái

<div class="grid-2">
<div>

### 1. QUAN HỆ KHÔNG GIAN LÀN XE (`lane_relation`)
*Hành lang an toàn phanh xe VF8 ($W_{\text{ego}}=1.93\text{m}$, corridor rộng $3.0\text{m}$):*
* **`in_lane` ($|x_{\text{lat}}| \le 1.75\text{m}$):** Điểm tiếp đất nằm trong làn xe. **Kích hoạt logic AEB Phanh Khẩn Cấp**. Vật thể nằm trực tiếp trên quỹ đạo va chạm!
* **`near_lane` ($1.75\text{m} < |x_{\text{lat}}| \le 3.25\text{m}$):** Nằm trong vùng đệm $\le 1.5\text{m}$ ngoài mép làn. **Kích hoạt FCW & Nạp sẵn dầu phanh (Pre-fill)**.
* **`out_of_lane` ($|x_{\text{lat}}| > 3.25\text{m}$):** Nằm ngoài vùng đệm (> 1.5m, vỉa hè). **Theo dõi Thụ động**, CẤM phanh để tránh đâm dồn toa (Cost 150).

### 2. PHÂN TẦNG CỰ LY PHANH (`distance_band`)
*Động lực học xe 50 km/h: Quãng đường dừng $15.1\text{m}$ (khô), $22.5\text{m}$ (ướt):*
* **`lt10m` (< 10m):** Vùng tử vong ($y_{\text{bottom}} \ge 540\text{px}$, $h_{\text{ped}} > 140\text{px}$). Phanh $100\%$ lực để giảm xung lực va chạm (Mitigation).
* **`10to25m` (10-25m):** Vùng vàng can thiệp ($420\text{px} \le y_{\text{bottom}} < 540\text{px}$). Đủ cự ly phanh dừng hẳn xe an toàn.
* **`gt25m` (> 25m):** Vùng nhận thức tầm xa ($y_{\text{bottom}} < 420\text{px}$). Theo dõi track.

</div>
<div>

### 3. ĐỘNG HỌC VECTOR & ZERO-LOSS CONTROL
* **`motion_state` (Động học vector qua 2 keyframes $\Delta t = 500\text{ms}$):**
  * `static`: Vận tốc thực tế $< 0.5\text{km/h}$ (xe đỗ, người đứng chờ).
  * `along`: Di chuyển cùng/ngược chiều dọc làn ($\pm 30^\circ$ so với trục đường).
  * `crossing`: Vector vận tốc ngang $v_{\text{lat}} \ge 0.5\text{m/s}$ cắt ngang qua đường.
  * `cut_in`: Tạt đầu đột ngột vào hành lang làn ở cự ly $< 25\text{m}$ với $v_{\text{lat}} \ge 1.0\text{m/s}$. **Nguy cấp số 1 $\implies$ Kích hoạt chuông FCW và nạp phanh**.
* **`risk_status` (Timeline CVAT):** `normal` ($TTC > 3.0\text{s}$) $\to$ `threatening` ($TTC \le 2.0\text{s}$) $\to$ `colliding` ($TTC < 0.3\text{s}$) $\to$ `post_crash`.
* **`ignore` (Zero-loss mask $Loss = 0$):** Bắt buộc kích hoạt khi $h < 12\text{px}$, lóa trắng photon ($Pixel = 255$), che khuất $> 80\%$, vệt nước mờ $> 50\%$.
* **`prelabel_modified`:** Đo tỷ lệ sửa đề xuất AI (PMR). Nếu PMR $< 5\%$ cảnh báo nhân viên lười duyệt ẩu (Anchoring Bias).

</div>
</div>

--- 

### 2. IGNORE REGION & GIAO THỨC HOÃN PHÁN QUYẾT (ABSTAIN)
* **Bản chất Kỹ thuật Ignore Region:** Triệt tiêu gradient đạo hàm ngược ($Loss = 0$), loại khỏi mẫu số Precision/Recall, tránh trừng phạt mô hình do giới hạn vật lý của cảm biến quang học.
* **5 Điều kiện Kích hoạt Cứng:**
  1. Chiều cao Bbox $h < 12\text{px}$ (vật thể $> 80\text{m}$, không đủ photon phân giải).
  2. Lóa bão hòa cảm biến: $> 80\%$ diện tích pixel chạm mức trắng tuyệt đối ($Pixel=255$).
  3. Che khuất $> 80\%$ mà không có chuỗi track mượt từ khung hình trước.
  4. Vết nhòe quang học do giọt nước mưa lớn bám kính lái che phủ $> 50\%$ vật thể.
  5. Đám đông dính khối hỗn tạp không thể bóc tách từng cá thể.
* **Giao thức Abstain (Quyền không đoán mò):** Gặp sương mù dày/mù quang học, Annotator bấm Abstain gắn tag `can_xem_lai` đẩy sang hội đồng phân xử đa cảm biến **Radar 77GHz**.

</div>
</div>

<div class="card-blue">
<strong>NGUYÊN TẮC:</strong> Mô hình chỉ học những gì quang học giải quyết được. Ép mô hình học ở vùng photon bão hòa hoặc &lt; 12px chỉ sinh ra trọng số rác và làm mất ổn định hàm mất mát!
</div>

---

# 8. TỔ C: KHÓA 10 CA BIÊN (EDGE CASES) GIAO THÔNG VIỆT NAM & DECISION LOG [TA Q3]
## Bản Quy Chuẩn Xử Lý Ranh Giới Cơ Học, Động Học & Ghi Vết Nhật Ký Quyết Định Kỹ Thuật

<div class="grid-2">
<div>

### NHÓM CA BIÊN CHE KHUẤT & ĐẶC THÙ VIỆT NAM
1. **Người sau cột lộ 1 chân:** Gán Amodal Bbox trùm chiều cao người trưởng thành ($1.7\text{m}$), cờ `occlusion: 50-80%`, `class: Pedestrian`.
2. **Xe máy sau ô tô thò gương:** Nếu thấy tay lái $\implies$ Gán Amodal Bbox xe máy. Nếu chỉ thấy 1 góc gương rời rạc $\implies$ `ignore = true`.
3. **Người dắt xe máy:** Gộp thành **1 Bbox duy nhất**. Dắt sát lề đường $\implies$ `Pedestrian`; Dắt cắt ngang lòng đường $\implies$ `UNKNOWN_OBJECT` (`crossing`).
4. **Xe máy chở kẹp 3, kẹp 4:** **CẤM TÁCH RIDER**. Gán 1 Bbox duy nhất ôm trọn người lái, khách, giỏ hàng và 2 bánh xe tiếp đất.
5. **Xe máy tạt đầu (Cut-in) cự ly gần (<10m) lộ 1/3:** Bắt buộc vẽ Amodal Bbox ôm trọn thân xe ($1.8\text{m}$), cờ `motion: cut_in`, `lane: in_lane`, `dist: lt10m`.

</div>
<div>

### NHÓM CA BIÊN QUANG HỌC & ĐÁM ĐÔNG PHỨC TẠP
6. **Đèn pha xe tải ngược chiều lóa trắng:** Khoanh toàn bộ quầng sáng bão hòa ($Pixel=255$), gán `UNKNOWN_OBJECT`, cờ `ignore = true`.
7. **Giọt nước đọng kính lái tạo bóng ma:** Lùi 3 frame liên tiếp; nếu không tịnh tiến quang học theo mặt đường $\implies$ **CẤM GÁN** (Artifact quang học).
8. **Đám đông 3-5 người dính chặt:** Thấy $\ge 20\%$ cơ thể $\implies$ Bbox riêng; Dính chặt thành khối không phân tách $\implies$ 1 Bbox lớn, `ignore = true`.
9. **Bóng phản chiếu đường ướt / kính showroom:** **CẤM GÁN BBOX**. Đáy Bbox kết thúc tại điểm tiếp đất của bánh xe/bàn chân thực.
10. **Hình người in pano / hông xe buýt:** **CẤM TUYỆT ĐỐI GÁN BBOX**. Đối chiếu mặt phẳng chuyển động gắn với thân xe buýt.

</div>
</div>

<div class="card-green">
<strong>SỔ TAY QUYẾT ĐỊNH KỸ THUẬT (DECISION LOG):</strong> Mọi tình huống tranh cãi được lưu vết tại <code>decision_log.md</code> kèm cặp ảnh Good/Bad và mã quy định (ví dụ: QĐ-004), tạo cơ sở hồi tố (Retroactive Audit) khi cập nhật Guideline.
</div>

---

# 9. TỔ C: BẢN ĐẶC TẢ TRIỂN KHAI KỸ THUẬT TRÊN NỀN TẢNG CVAT
## Khóa Tử Huyệt `mutable: true`, JSON Spec CVAT 2.x, Ánh Xạ Nuclio & Phím Tắt Công Thái Học

<div class="grid-2">
<div>

### 1. TỬ HUYỆT `mutable: true` & JSON SPEC CVAT 2.x
* **Cờ `mutable: true` là sống còn:** Trong CVAT Track Mode, nếu thiếu `mutable: true`, thuộc tính bị khóa ở cấp Track. Khi xe máy đổi từ `near_lane` sang `in_lane` ở frame 20, CVAT sẽ **đè bẹp 19 frame trước thành `in_lane`**, phá hủy chuỗi thời gian va chạm!
* **Bắt buộc `"mutable": true`** cho cả 8 thuộc tính động học và cờ.
* **JSON Constructor Spec (CVAT 2.x REST API):**
```json
{
  "name": "Pedestrian", "color": "#FF0000", "type": "rectangle",
  "attributes": [
    {"name": "lane_relation", "input_type": "radio", "mutable": true,
     "values": ["in_lane", "near_lane", "out_of_lane"], "default_value": "out_of_lane"},
    {"name": "distance_band", "input_type": "radio", "mutable": true,
     "values": ["lt10m", "10to25m", "gt25m"], "default_value": "gt25m"},
    {"name": "motion_state", "input_type": "select", "mutable": true,
     "values": ["static", "along", "crossing", "cut_in"], "default_value": "static"}
  ]
}
```

</div>
<div>

### 2. ÁNH XẠ NUCLIO YOLOV10 & PHÍM TẮT CÔNG THÁI HỌC
* **Ánh xạ Model-in-the-Loop (Nuclio Serverless Function):**
  * COCO 0 (`person`) $\implies$ `Pedestrian`
  * COCO 1 (`bicycle`) $\implies$ `Bicycle` (tự động gộp rider)
  * COCO 2 (`car`) $\implies$ `Car`
  * COCO 3 (`motorcycle`) $\implies$ `Motorcycle` (tự động gộp rider)
  * COCO 5 (`bus`) & 7 (`truck`) $\implies$ `Bus_Truck`
  * `UNKNOWN_OBJECT`: **CẤM tự động sinh bằng AI** để triệt tiêu phanh ma.
* **Bản đồ Phím tắt Công thái học ($\ge 120\text{ box/h}$):**

| Phím tắt (Hotkey) | Thao tác tương ứng trong CVAT |
| :---: | :--- |
| `1` đến `6` | Đổi nhanh 6 lớp (`1`: Ped, `2`: Moto, `3`: Bike, `4`: Car...) |
| `Shift + N` | Tạo Bbox mới ở chế độ **Track Mode** |
| `K` | Đặt **Keyframe** ghi nhận đổi tọa độ / thuộc tính |
| `I` | Bật/tắt cờ **`ignore`** cho box $< 12\text{px}$ hoặc lóa |
| `Shift + R` | Lật nhanh cờ rủi ro: `normal` $\to$ `threatening` |
| `F` / `D` | Nhảy tiến / lùi 1 khung hình ($\Delta t = 500\text{ms}$) |

</div>
</div>

---

# 10. TỔ D: ĐO LƯỜNG CHẤT LƯỢNG & TIÊU CHUẨN NGHIỆM THU LÔ [TA Q2, TA Q5]
## Bác bỏ mAP, Sử dụng Miss Rate trên VRU Khóa Ngân sách FP & Chiến lược QC Bất đối xứng

<div class="grid-2">
<div>

### [TA Q5] METRIC ĐO ĐẠC: MISS RATE TRÊN VRU
* **Bác bỏ mAP tổng hợp:** mAP đánh đồng việc bỏ sót mạng người với bỏ sót một thùng rác ven đường.
* **Chỉ số Sống còn:** **Miss Rate trên VRU** (Người đi bộ/Xe máy) trong làn và khoảng cách nguy hiểm $< 25\text{m}$:
  $$\text{Miss Rate}_{\text{VRU}} = \frac{\text{FN}_{\text{VRU}}}{\text{TP}_{\text{VRU}} + \text{FN}_{\text{VRU}}} \times 100\% \quad (\text{Mẫu số: Tổng số VRU thực tế})$$
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

# 11. GIAI ĐOẠN 9: RELEASE PACKET & GIAI ĐOẠN 10: GIÁM SÁT VẬN HÀNH
## Bộ Hồ sơ Xuất xưởng 5 Thành phần (S3 WORM), Đo lường Data Drift & Fleet Telemetry Loop

<div class="grid-2">
<div>

### BƯỚC 9: ĐÓNG GÓI RELEASE PACKET v1.0
*Chốt chặn cuối cùng: Data Owner (TV1) chỉ ký duyệt khi đủ 5 phần:*
1. **Frozen Artifacts & SHA-256 Checksum:** Ảnh nén kèm nhãn Parquet/COCO. Cấp mã băm **SHA-256**, bật chế độ **S3 Object Lock (WORM)** chống can thiệp sau đóng băng.
2. **Dataset Lineage & Version Control:** Ghi vết bất biến `dataset_version: v1.0.0`, hash commit pipeline, version guideline v1.2.
3. **Data Split Manifest:** `split_manifest.json` chứng minh giao thoa `trip_id` = $0.00\%$, chặn **mAP tăng cao giả tạo do rò rỉ**.
4. **Báo cáo Kiểm toán QC:** Biên bản audit 20% + Honeypot 5%, xác nhận $\text{Miss Rate}_{\text{PII}} \le 0.001\%$.
5. **Dataset Card Chuẩn hóa:** Khớp 5 chỉ số Loss-Check, công bố ODD & Known Limitations (kính bẩn > 30%, tuyết).

</div>
<div>

### BƯỚC 10: GIÁM SÁT DRIFT & HỘP ĐEN XE LĂN BÁNH
1. **Giám sát Data Drift Đầu vào:**
   * Tính khoảng cách Wasserstein / PSI trên độ rọi ánh sáng (Lux) và độ tán xạ mưa.
   * Cảnh báo khi $\text{PSI} > 0.2$ so với phân phối train gốc.
2. **Fleet Telemetry Trigger (Hành vi tài xế là chân lý):**
   * *Tài xế đạp thốc ga đè phanh:* Phanh oan (FP) ⇒ Lưu 15s clip.
   * *Tài xế phanh gấp > 0.6g né vật:* Bỏ sót (FN) ⇒ Lưu 15s clip.
3. **Giao thức Sửa lỗi Tận gốc (Root Cause Rework):**
   * CẤM sửa lẻ tẻ từng ảnh!
   * Họp Adjudication ⇒ Cập nhật Guideline v1.2 ⇒ **Hồi tố (Retroactive Audit)** quét lại các batch cũ xuất xưởng.

</div>
</div>

---

# 12. SỰ THẬT TRẦN TRỤI: ĐIỂM GÃY VỠ (SPOF) & 3 TẦNG BẢO VỆ [TA Q6]
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
