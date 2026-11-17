# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 509; đối tượng sau duyệt: 472; ngưỡng accepted: IoU >= 0.9
- Đã **loại 24 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 436 | 436/509 = 85.7% | 509 box máy |
| edited (sửa) | 17 | 17/509 = 3.3% | 509 box máy |
| deleted (xoá) | 56 | 56/509 = 11.0% | 509 box máy |
| added (vẽ thêm) | 19 | 19/472 = 4.0% | 472 đối tượng sau duyệt |

Trong số deleted + added, **12 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 7.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 250 | 3 | 13 | 0 |
| bicycle | 6 | 0 | 2 | 1 |
| motorbike | 121 | 5 | 2 | 3 |
| car | 51 | 0 | 4 | 5 |
| bus | 2 | 0 | 1 | 3 |
| truck | 0 | 0 | 6 | 3 |
| three_wheeler | 6 | 9 | 28 | 4 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 53 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 43 | 21 | 10 | 67.2% (KTC 95%: 55–77%) | 81.1% (KTC 95%: 69–89%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 10/12 = 83.3% (KTC 95%: 55–95%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - wrong_class: 3/3
  - loose: 3/3
  - extra: 3/3
  - missing: 1/3
  - lọt: img_021.jpg (missing), img_005.jpg (missing)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
