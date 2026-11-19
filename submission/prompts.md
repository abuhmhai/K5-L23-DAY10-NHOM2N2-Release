# Dùng AI

| # | Ai dùng | Công cụ | Prompt (tóm tắt) | AI sai ở đâu | Nhóm kiểm lại thế nào |
|---|---|---|---|---|---|
| 1 | @haihm | Codex | Đọc đề, hướng dẫn và chuẩn bị lab trong repo HMH | Ngưỡng đối chiếu có thể báo thừa hoặc bỏ sót | Chạy lô sạch 50 ảnh, tái chạy seed 11, kiểm tra handover trên dữ liệu giả lập |
| 2 | @haihm | Codex | Hỗ trợ xem ảnh và điền ghi chú review | Gợi ý của AI cần người xem đối chiếu | Hoàng Minh Hải (@haihm) mở và xác nhận 3 ảnh nghi lỗi nhãn, 2 ảnh sai hash và ảnh minh họa bảo mật |

## Phiên hỗ trợ Codex

- Yêu cầu người dùng là đọc repo Day 10, hướng dẫn và bắt đầu triển khai bài lab.
- Codex viết validator, công cụ dựng hàng đợi ảnh, truy vết ticket và tạo lô v2, đồng thời soạn hướng dẫn chạy.
- Đã kiểm thử bằng bộ dữ liệu giả lập riêng của Codex, gồm cả tình huống lệch lớp, lệch box, thiếu box và hai vật dùng chung segment. Bộ kiểm handover đạt 17/17 trên bộ kiểm thử giả lập.
- Codex ghi kết luận theo xác nhận của Hoàng Minh Hải, chạy kiểm định dạng trên lô chính thức (17 PASS, 0 FAIL), tự chấm và export seed 11.
