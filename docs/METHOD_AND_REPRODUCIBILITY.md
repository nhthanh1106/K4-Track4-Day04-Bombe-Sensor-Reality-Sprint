# Method và khả năng tái hiện

## Nguồn

- Shiva Aher, *Safety-Critical Camera Reliability Monitoring for ADAS via Degradation-Aware Uncertainty Pattern Analysis*: https://arxiv.org/html/2605.05439v1. Bản v1 ngày 06/05/2026; preprint. Kiểm tra trang nguồn ngày 05/10/2026.
- Paper nhận một ảnh RGB, dự đoán loại/mức degradation, GSHI và spatial uncertainty. Paper báo cáo trên dữ liệu KITTI-derived, dùng health estimation MAE và early-warning analysis. Nhóm chỉ dùng công thức GSHI và ý tưởng severity sweep; không tái hiện learned monitor, training hay số đo tác giả. Trọng số hiệu dụng lấy từ Table I như ghi trong notebook.
- Input demo: https://ultralytics.com/images/bus.jpg. Nguồn dự phòng được notebook khai báo: https://raw.githubusercontent.com/ultralytics/ultralytics/main/ultralytics/assets/bus.jpg.
- SHA256 ảnh gốc trong config: `c02019c4979c191eb739ddd944445ef408dad5679acab6fd520ef9d434bfbc63`. Ảnh gốc chưa có trong gói đã cung cấp; các ảnh `examples/` đã có annotation, không dùng làm đầu vào sạch để tái chạy.
- Detector: Ultralytics YOLOv8n, `yolov8n.pt`; version package ghi trong config là 8.4.173. Chưa có commit repository/weight hash.

## Đường chạy tối thiểu

Google Colab, mở notebook và chạy tuần tự mọi cell. Không cần training. Input là bus.jpg, resize giữ tỷ lệ với cạnh dài 640 px. Output là CSV metric, ảnh annotation, plots và cấu hình. Runtime GPU/CPU được notebook tự chọn; thiết bị và thời gian thực tế của lần chạy cũ chưa có log riêng.

Seed 42 và `seed_for(name) = 42 + int(SHA256(name)[:8],16)` để tạo noise. Mỗi severity dùng cùng seed, tức cùng mẫu noise chuẩn được scale theo sigma, không phải các lần lặp độc lập. Có 1 ảnh, 1 baseline duy nhất và 20 điều kiện lỗi, tổng 21 dòng raw. Summary thêm baseline vào mỗi mode để vẽ sweep nên có 25 dòng; không phải 25 ảnh độc lập.

| Mode | Mapping severity s |
| --- | --- |
| motion_blur | Kernel ngang k = 1 + 2 × int(round(15s)), lần lượt 1/9/17/23/31 px |
| low_light | Nhân intensity với 1 − 0,9s, chuyển uint8 |
| sensor_noise | Gaussian sigma = 40s trên thang 0–255, clipping |
| exposure_shift | Nhân intensity với 1 + 2s, clipping |
| blur_noise | Blur rồi noise, cùng severity s; lỗi phối hợp có chủ đích |

Giữ nguyên ảnh, detector, classes [0,1,2,3,5,7], imgsz 640, confidence 0,25 và IoU matching 0,5. Các mode riêng kiểm tra một yếu tố; blur_noise là thử tương tác, không quy riêng tác động cho blur hoặc noise.

## Metric

- Laplacian variance: `var(cv2.Laplacian(gray, CV_64F))`, phụ thuộc thang pixel và resize. Thường giảm khi blur, nhưng noise có thể làm tăng.
- Saturation/dark ratio: tỷ lệ gray ≥250 / gray ≤20, đơn vị tỷ lệ 0–1.
- Mean brightness: mean(gray)/255, 0–1. Entropy: `−Σ p log2(p)`, bits.
- Health proxy: `min(1,L/Lclean) × min(1,E/Eclean)`, với E = max(0,1−dark−saturation). Các mẫu có denominator khác 0; code chặn denominator ở 1e−9. Cần ảnh sạch, 0–1, cao hơn nghĩa proxy đánh giá khỏe hơn.
- GSHI target: `product((1−s_i)**weight_i)`, weights 1,54 / 1,30 / 1,05 / 0,95. Target tính từ severity biết trước, không phải dự đoán sức khỏe từ ảnh.
- Clean-box retention: ghép một-một theo class và IoU ≥0,5 với prediction sạch, chia số box sạch. Cao hơn là nhất quán hơn với baseline, không phải recall ground truth.
- Matched confidence: tổng confidence các box ghép chia số box sạch; box mất đóng góp 0. Mean confidence chỉ tính các detection còn lại nên có survivor bias.

`_std` thiếu vì n=1, không coi là std=0. Mean confidence thiếu khi không có detection. Ngưỡng proxy 0,6 là heuristic lab chưa hiệu chuẩn; việc vượt ngưỡng ở severity s là quan sát sweep, không phải latency hay lead time theo thời gian.

## Chứng cứ và giới hạn

Số liệu trong báo cáo đọc từ CSV đã cung cấp. Manifest ghi SHA256 file hiện có để kiểm tra toàn vẹn; không phải commit/version của lần chạy gốc. Không có bằng chứng nhóm đã hoàn tất lượt nộp VLearn. Giới hạn của benchmark nhóm: một ảnh, thiếu nhãn thật, simulator giản lược, chưa đo end-to-end latency, camera thật hay tính khái quát. Những giới hạn này là đánh giá phép thử nhóm, không tự gán cho tác giả paper.
