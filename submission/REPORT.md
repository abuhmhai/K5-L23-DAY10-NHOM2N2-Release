# Báo cáo Day 10 — Nhóm HMH

## 1. Số liệu luyện tập

| Chỉ số | Kết quả |
|---|---|
| Seed 11, 250 ảnh | 13 lỗi luật, 3 ảnh nghi lỗi nhãn, 2 ảnh sai hash nằm trong số 13 lỗi luật |
| Lô sạch calib, 50 ảnh | 0 lỗi luật, 0 ứng viên |
| Điểm làm tròn seed 11 | F1 lỗi luật ≈ 1,0 và F1 tổng ≈ 0,8. Lần tự đo dùng danh sách lỗi luật. |
| Kết quả seed 11 sau review | 16 lỗi trên 250 ảnh (6,4%). F1 tổng ≈ 0,9, lỗi luật ≈ 1,0, lỗi gán nhãn ≈ 0,8. |
| Seed kiểm tra bổ sung 22 | 9 lỗi luật, F1 lỗi luật ≈ 1,0 trước review. |

Validator ghép YOLO và panoptic theo tên lớp, IoU và phần pixel của segment nằm trong box. Ngưỡng lọc bất đồng lớp là IoU từ 0,62 và độ phủ mask từ 0,85. Một cặp cùng lớp nghi lệch box khi độ phủ dưới 0,91 và kích thước tương đương. Với nghi thiếu box, ưu tiên segment đủ lớn. Đo nhiễu trên 50 ảnh calib và dùng ảnh giao để kiểm tra từng ứng viên. Các ngưỡng là bộ lọc để mở ảnh, chưa cho biết recall trên đề ẩn.

## 2. Vì sao báo hết ứng viên làm F1 giảm?

F1 phụ thuộc vào precision và recall. Hai nguồn nhãn có thể lệch tự nhiên, chẳng hạn vật bị che hoặc cách nhóm instance khác nhau. Codex đưa ra 3 ứng viên ở seed 11. Hoàng Minh Hải (@haihm) đã mở ảnh, xác nhận cả 3 và không bác bỏ ca nào. Việc báo tự động cả hàng đợi có thể tăng số báo thừa ở seed khác, khiến precision và F1 giảm khi số lỗi phát hiện đúng giữ nguyên.

## 3. Quyết định và lineage

Release v1 `lab250_1c19fc38201a` có 13/250 lỗi luật theo cách đếm của bài, tương đương 5,2%, vượt ngưỡng 2%. Lineage đã ghi `HOLD`. Truy vấn ảnh hưởng trả về `R-021: affected=True action=review_or_retrain`. Việc thay lô dữ liệu không thay đổi trọng số mô hình đã train. Team AI cần xem xét lại hoặc train lại run bị ảnh hưởng.

Sau review, v1 có **16/250 lỗi (6,4%)** và giữ quyết định `HOLD`. Release `lab250_1c19fc38201a_v2` ghi v1 làm parent, giữ **243 ảnh với 1.683 nhãn detection** và chuyển **7 ảnh** vào hàng đợi xử lý lại. Số liệu lấy từ `errors.json`, `manifest.csv`, `label_summary.csv` và `rework_queue.csv`.

## 4. Hash chứng minh gì?

SHA-256 khớp cho phép kiểm tra file nhận được có đúng chuỗi byte đã ghi trong manifest hay đã bị thay đổi. Chất lượng hình ảnh, độ chính xác của nhãn và sự tuân thủ guideline cần được kiểm riêng. Hoàng Minh Hải đã mở hai JPEG sai hash và xác nhận ảnh còn nguyên. Hai ảnh được giữ lại trong v2 với SHA-256 tính lại từ dữ liệu thực tế.

## 5. Truy vết

Tìm các ảnh có ít nhất một box detection lớp `wine glass` thuộc job dùng guideline v1.2. Từ v1.3, lớp này vẫn phải gán box khi bị che trên 50% hoặc bị cắt ở mép. Tầng A gồm **3 ảnh** trong hai cảnh ticket nêu (`scene_562` và `scene_576`). Tầng B gồm **4 ảnh** đủ điều kiện ở các cảnh khác. Mã cần báo là `A01`, `A05`, `Q02`.

Phiên bản lấy theo `jobs.csv` vì trường guideline trên ảnh có thể sai và chính là một loại lỗi validator kiểm tra. Điểm truy vết trước review làm tròn ≈ 1,0 cho từng mục.

## 6. Xác minh và giới hạn

Validator và truy vết tái tạo được cùng đầu ra trên seed 11. Lô sạch không có lỗi luật. Sau khi Hoàng Minh Hải xác nhận 3 ảnh nghi lỗi nhãn, 2 ảnh sai hash và ảnh minh họa bảo mật, lô chính thức đạt **17 PASS, 0 FAIL** ở bộ kiểm bàn giao. Điểm đề ẩn chưa có.

## 7. Kiểm tra phương án cho đề ẩn

Bổ sung ứng viên cho box dịch nhẹ dù tỷ lệ phủ mask còn cao, segment bị bỏ sót cạnh box đã ghép cho đối tượng khác và segment gộp có hai box đè nhau nhưng vẫn có vùng pixel riêng đủ lớn. Khâu đối chiếu bỏ qua crowd. Kiểm lỗi luật về diện tích vẫn đối chiếu pixel đối với segment thing có đánh dấu crowd.

Kết quả trên dữ liệu luyện tập công khai giữ nguyên lỗi luật của seed 11, 22, 33 và 91 so với trước thay đổi. Chấm riêng 33 và 91 cho F1 lỗi luật làm tròn ≈ 1,0. Lô sạch vẫn có 0 lỗi luật, 0 ứng viên. Hồ sơ đã export của seed 11 tái tạo giống hệt. Chưa có số đo trên đề ẩn.
