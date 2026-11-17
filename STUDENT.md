# Thông tin học viên

- Họ và tên: Hoàng Hải Đăng
- MSHV: 2A202603189
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: hoanghaidang1233
- Repo bài nộp (URL): https://github.com/hoanghaidang1233/K5-L23-Day08-2A202603189-HoangHaiDang-AI-Assisted-Labeling
- Commit chốt bài (hash): <<>>
- Máy: Windows 11, NVIDIA GeForce RTX 3050 Laptop GPU (CUDA)
- CVAT (Docker): v2.74.1

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted / edited / deleted: 436 / 17 / 56 (mẫu số: 509 box máy) = 85.7% / 3.3% / 11.0%; added: 19 (mẫu số: 472 đối tượng) = 4.0%
- So với gold: precision / recall của **máy** 43/64 = 67.2% / 43/53 = 81.1%
- Bắt bẫy: 9/12 = 75.0%
- Thời gian: gán tay 3.0 phút/ảnh (12'/4); duyệt có pre-label 3.1 phút/ảnh (75'/24)
- Lỗi QC: 20.0% theo ảnh (1/5), 1.0% theo đối tượng (1/97); cận trên 95% theo ảnh 62%
- **Quyết định:** Rework — lý do một dòng: lỗi QC 1/5 ảnh = 20% > 2%, bắt bẫy 9/12 = 75% < 80%, lô cần review lại
