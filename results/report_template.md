# Camera degradation health score — báo cáo benchmark
## Bài toán và phạm vi
Xe ADAS; sensor camera RGB. 1 ảnh/frame, 5 mức severity mỗi loại, 21 samples.
Paper tham chiếu: https://arxiv.org/html/2605.05439v1 (preprint; không khẳng định đã accept).
## Phần tái hiện
Công thức GSHI target, effective weights Table I; severity sweep. Phép sinh degradation giản lược do nhóm chọn.
Không tái hiện CNN monitor, training hoặc mAP/lead time tác giả báo cáo.
## Metric
Laplacian variance, saturation ratio, dark ratio, brightness, entropy; reference-based health proxy.
YOLO loaded: True. Detector proxies: count, mean confidence, clean-box retention, matched confidence.
Không có ground truth: detector consistency không phải precision/recall/mAP và có thể thừa hưởng lỗi clean prediction.
## Kết quả nhóm tự đo
Xem summary.csv, per_image_metrics.csv, before_after.png, metric_curves.png.
Ngưỡng health proxy: 0.6, do nhóm chọn, chưa hiệu chuẩn. Xem proxy_alerts.csv.
## Nhận xét cần điền sau khi xem kết quả
- Loại degradation nào làm metric thay đổi rõ nhất? Trích số cụ thể từ CSV.
- Blur+noise có làm Laplacian/health proxy tăng so với blur không? Nếu không, báo cáo đúng dữ liệu quan sát.
- Detection có mất khi severity tăng? Chỉ mô tả consistency, không suy ra accuracy.
- Khi proxy thấp hoặc vùng quan trọng mất chi tiết, đề xuất giảm trọng số camera và kiểm tra sensor khác;
  đây là đề xuất lab, không phải chính sách safety đã xác minh.
## Giới hạn
Tập nhỏ; clean-reference proxy; simulator mappings tự chọn; không đánh giá dữ liệu thực adverse weather;
GSHI target giảm theo severity do công thức, không chứng minh monitor dự đoán được từ ảnh.
Mặc định một ảnh mẫu đường phố: case study, không đủ chứng minh generalization. Không so trực tiếp trị số với paper do khác dữ liệu/mapping/evaluation.
