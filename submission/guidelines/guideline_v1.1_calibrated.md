# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603189 - Hoàng Hải Đăng

> **Chỉ viết phần thay đổi so với v1.0** (không copy cả file). Chốt các câu hỏi mở ở mục 6 của v1.0 (hoặc nói rõ câu nào chưa chốt, vì sao)
> và thêm quy tắc mới rút ra từ lỗi của máy / lỗi của bạn. Mỗi ca khó **có ảnh ví dụ** (`img_0xx`) và khớp một dòng trong `qc/calibration_decisions.csv`.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 2 | Khối xe / người nhỏ dày đặc: từng vật hay `ignore_region`? Ngưỡng cụ thể? | Sửa mục 4.1.6: vật cao < 20 px **đứng lẻ** thì không gán; **cụm ≥ 5 vật** cao < 20 px nằm sát nhau thì vẽ **một** `ignore_region` bao cả cụm. Vật ≥ 20 px vẫn gán từng vật dù nằm trong khối dày | img_036, img_001 | CAL-02 |
| 3, 4 | Mask xe có người lái / người ngồi: mask xe gồm người không? | Bổ sung mục 4.2.4: mask xe **không** gồm người lái / người ngồi; phần người che lên xe thuộc mask `person` (vật phía trước). Khi dùng SAM phải đặt click âm vào người | img_010, img_013 | CAL-06 |

Câu hỏi mở 1, 5, 6, 7 **chưa chốt** ở v1.1: lô 24 ảnh không có ca đủ rõ để quyết (không tick `can_review` box nào).

## 2. Ca khó mới (từ lỗi của máy, bẫy lọt, box `can_review`)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| img_011, img_002, img_013 | Xe ba bánh có mui / thùng, có chiếc bị cắt mép ảnh | YOLO26m gọi `truck` (img_011 0.77, img_002 0.73) và `car` (img_013 0.41); review lô: `truck` 0 accepted / 6 deleted | Luôn gán `three_wheeler`; mọi box `car` / `truck` phải kiểm số bánh khi review | Mục 2 (dòng `three_wheeler`): "kể cả bị cắt mép ảnh"; mục 4.3: 3 bánh → `three_wheeler`, ≥ 4 bánh → `car` / `truck`, không đếm được → `can_review` (CAL-01) |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Mục 2 + 4.3: xe ba bánh (kể cả cắt mép) luôn `three_wheeler`, đếm bánh khi phân vân car / truck · Mục 4.1.6: vật < 20 px lẻ thì bỏ, cụm ≥ 5 vật thì một `ignore_region` · Mục 4.2.4: mask xe không gồm người lái | CAL-01 (lỗi máy), CAL-02 (câu hỏi mở 2), CAL-06 (câu hỏi mở 3, 4) |
