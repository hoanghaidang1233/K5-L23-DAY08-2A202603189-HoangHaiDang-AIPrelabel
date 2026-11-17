# Báo cáo Day 08 — 2A202603189 - Hoàng Hải Đăng

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| motorbike | YOLO26m (`--size m`) | **0.35** | `threshold_sweep`: recall 11/12 = 92%, precision 11/11 = 100% (12 vật gold). Ở 0.5 recall còn 10/12 (r02 cũng thấy tăng ngưỡng làm mất xe máy thật, img_007) |
| person | YOLO26m | 0.3 (mặc định) | Không ngưỡng nào ≤ 0.5 đạt recall 90% (tối đa 16/21 = 76% ở 0.15). Ở 0.3: precision 15/29 = 52%, recall 15/21 = 71%. Hạ ngưỡng chỉ thêm 1 TP nhưng thêm 4 FP → giữ 0.3 và bù bằng review (bước "bỏ sót") |
| car | YOLO26m | 0.3 (mặc định) | Tối đa recall 7/8 = 88%, không đổi từ 0.1 đến 0.5; ở 0.3 precision 7/13 = 54% (6 FP); lên 0.5 vẫn còn 4 FP (precision 64%), đến 0.9 mới hết FP nhưng recall còn 4/8. FP điểm cao khó lọc bằng ngưỡng → có thể là lỗi sai lớp giống ở pilot (xe ba bánh / van gọi `car`: r01 img_016, r04 img_004, img_013), cần kiểm ở review |
| bicycle / bus / truck | YOLO26m | 0.3 (mặc định) | Quá ít vật trong gold (bicycle 4, bus 0, truck 1 < 5): không chọn ngưỡng riêng. `truck`: recall 0/1 ở mọi ngưỡng, FP 2 ở 0.3 (xe ba bánh gọi truck, r01 img_011, img_002) |
| three_wheeler | YOLOE-26s, prompt `three_wheeler` | **0.15** | recall 7/7 = 100%, precision 7/9 = 78%. Ở 0.3 recall chỉ 5/7 = 71% → hạ ngưỡng vì sót xe ba bánh tốn hơn xoá 2 box thừa khi review (r03, r06) |

Chuỗi ngưỡng dùng ở Part 4 (từ `qc/threshold_sweep.md`): `--thr "motorbike=0.35,three_wheeler=0.15"`, các lớp khác `--conf 0.3`.

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): <<điền ở Part 4>>
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: SAM 2.1 (`sam2.1_b.pt`). Máy có GPU RTX 3050 (CUDA) chạy được: 2.21 s/ảnh ở r04 (gồm cả YOLO26 + SAM), là bản lab khuyến nghị cân bằng chất lượng / tốc độ. Không cần SAM 3 (gated). Chưa so trực tiếp với MobileSAM.
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? Xe ba bánh bị YOLO26 gọi sai lớp với điểm cao: img_011 `truck 0.77`, img_002 `truck 0.73`; box 0.77 vẫn còn khi tăng conf lên 0.5 (r02). Đây là lỗi **sai lớp** (COCO không có lớp xe ba bánh) nên ngưỡng không sửa được. Cách sửa: dùng YOLOE prompt `three_wheeler` cho lớp này, merge với YOLO26 (YOLOE đặt trước), và ở bước review kiểm mọi box `car`/`truck` xem có phải xe ba bánh không.
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): điểm cao mà sai: img_011 `truck 0.77` (xe ba bánh), img_016 `car 0.73` (xe van), img_002 `truck 0.73` (xe ba bánh). Điểm thấp mà đúng: khi tăng conf 0.25 → 0.5 (r02) mất 75 box, chủ yếu người / xe máy thật ở xa (img_007: 9 person, 6 motorbike; img_011: 8 person).
- **Điểm 0.9 có phải đúng 90% không?** Không. `conf` là độ tự tin của model, không phải xác suất đúng; ví dụ `truck 0.77` ở img_011 vẫn sai lớp.
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: ngưỡng được chọn và chấm trên cùng 4 ảnh gold nên precision / recall ở trên là **lạc quan**; mẫu rất nhỏ (motorbike 12, three_wheeler 7 vật: một vật đổi recall 8–14 điểm %); bicycle / bus / truck không đủ vật để chọn ngưỡng riêng.

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | <<>> | <<>> | <<box máy>> |
| edited | <<>> | <<>> | <<>> |
| deleted | <<>> | <<>> | <<>> |
| added | <<>> | <<>> | <<đối tượng sau duyệt>> |

- Lớp nào máy tệ nhất? Vì sao (dẫn ảnh)? <<>>
- So với gold: precision / recall của **máy** <<>> (mẫu số: <<đối tượng gold>>)

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt bẫy: <<.../12>>; loại nào lọt: <<>>; vì sao (bạn đã bỏ bước nào của checklist)? <<>>
- Accepted cao mà bắt bẫy thấp (hoặc ngược lại) nói gì về cách bạn duyệt? <<>>

## 4. Máy có đáng dùng không?

- Thời gian (từ `time_log.csv`): gán tay <<... phút/ảnh>> vs duyệt <<... phút/ảnh>>. Thiên lệch (bạn gán gold trước nên đã quen cảnh): <<>>
- Kết luận: <<có / không / chỉ cho lớp nào>>, dựa trên <<số liệu>>
- **Ba quy tắc giảm thiên kiến của bạn** (mỗi quy tắc kiểm tra được bằng số hoặc việc làm):
  1. <<>>
  2. <<>>
  3. <<>>

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| <<img_0xx>> | <<>> | <<>> | <<>> | <<>> | <<>> |

- Đối chiếu guideline mục 4.2: quy tắc nào giúp, quy tắc nào thiếu? <<>>
- *(Tuỳ chọn)* Interactor SAM trong CVAT: làm được / không (lỗi gì, đã thử gì); khác gì offline: <<>>

## 6. Calibration (Part 6)

- Số quyết định: <<>> (`calibration_decisions.csv`); quan trọng nhất (dẫn `CAL-xx`): <<>>
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: <<>>
- Nguyên nhân gốc: <<guideline mơ hồ / chưa nắm quy tắc / máy>>

## 7. QC và quyết định (Part 6)

- Mẫu: <<n>> ảnh (<<m>> ảnh `can_review` + <<k>> ngẫu nhiên, seed 42); người kiểm: bạn (**hạn chế**: bạn vừa là người duyệt).
- Lỗi theo **đối tượng**: <<.../... = ...%>> · theo **ảnh**: <<.../... = ...%>> (ngưỡng Release ≤ 2%)
- Top lỗi (trong: bỏ sót, thừa, sai lớp, sai vị trí/kích thước, sai thuộc tính, sai mask) + một ảnh ví dụ mỗi loại: <<>>
- **Giới hạn mẫu nhỏ** (cận trên 95% trong `qc_report_auto.md`): <<>>
- Script kiểm được / không kiểm được (ví dụ ôm sát, cùng bỏ sót với máy, theo checklist thật): <<>>

**Quyết định: <<Release / Hold / Rework>>**, ngày <<YYYY-MM-DD>>. Lý do (đi theo Hold → Rework → Release): <<>>
Nếu Rework / Hold, việc cần làm tiếp: <<>>

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): 
  Trả lời: Claude Code (trợ lý lập trình) để đọc tài liệu lab, giải thích các bước, sửa bug `make_cvat_labels.py` và soạn nháp ghi chú Part 1 trong mục này. Kiểm tra: dán lại nhãn vào CVAT thành công, `validate_labels.py --all` OK (4 ảnh, 61 đối tượng), tự đọc lại từng dòng. AI **không** nhìn ảnh gold và không gợi ý nhãn.
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? 
  Trả lời: Không (đã gán tay trong CVAT, không chạy model hay AI Tools lên 4 ảnh gold).
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: 
  Trả lời: Windows 11, NVIDIA GeForce RTX 3050 Laptop GPU (CUDA), CVAT Docker **v2.74.1** (không phải 2.81 như slide), Python 3.13 (repo khuyến nghị 3.10–3.12). Server CVAT có cài YOLO26n và MobileSAM (Nuclio) nhưng **không** dùng cho Task gold. Vướng mắc: tab Raw báo `Attribute: "conf": attribute values must be a non-empty array` (xem dòng bug script).
- Dùng cache `data/cache/` thay vì tự chạy? <<không / có, vì ...>>
- Lỗi / bug script đã gặp (nếu có): 
  Trả lời: Lỗi / bug script: make_cvat_labels.py sinh values: [] nên CVAT báo lỗi “attribute values must be a non-empty array”. Đã sửa thành values = [default_value] (`[""]` cho `conf`, `["false"]` cho `can_review`, giống UI CVAT tự sinh), sinh lại `cvat/labels_generated.json`; không đổi tên nhãn hay mục `rules`.
- **Part 1 — gold gán tay:** 4 ảnh, 61 đối tượng, 12 phút (`time_log.csv`). `img_001`: 8 motorbike, 4 person, 1 car, 1 bicycle, 5 ignore_region · `img_012`: 6 person, 4 three_wheeler, 3 car · `img_020`: 4 car, 3 person, 3 three_wheeler, 1 truck · `img_036`: 8 person, 4 motorbike, 3 bicycle, 3 ignore_region. Không tick `can_review` box nào.
- **Ca khó Part 1 (nguyên liệu calibration):**
  - `img_001`, `img_036`: lần export đầu có 4 box cao < 20 px (`img_001` person 16 px; `img_036` car 14 px, car 12 px, bicycle 19 px), `validate_labels` cảnh báo; đã sửa theo guideline 4.1.6, validate sạch. Ngưỡng 20 px khó ước lượng bằng mắt (bicycle 19 px sát ngưỡng) → liên quan câu hỏi mở 2.
  - `img_001`: 5 `ignore_region` nhỏ nằm gần nhau (cao 15–32 px). Guideline 4.1.6 nói "một `ignore_region` bao cụm ≥ 5 vật" nhưng không nói khi nhiều cụm nhỏ nằm rời thì vẽ một vùng lớn hay nhiều vùng → cần chốt ở v1.1.
- **Vì sao gold phải gán trước khi thấy nhãn máy (CP1):** nếu đã thấy box máy, người gán dễ bị "neo": chấp nhận những vật máy tìm ra, vẽ box theo vị trí / kích thước của máy, và bỏ sót cùng những vật máy bỏ sót. Gold khi đó giống máy hơn thực tế, nên precision / recall của máy đo trên gold bị **thổi phồng** (nhất là recall: vật máy sót không có trong gold thì không bị tính là sót), và mất chuẩn độc lập để đo thiên kiến tự động hóa ở Part 4.
- Một điều bạn làm khác lần sau: <<>>
