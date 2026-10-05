# Lab Ngày 4 — Sensor Reality Sprint

**Chủ đề trong notebook:** T1, Camera degradation health score. **Nền tảng:** xe ADAS. **Sensor:** camera RGB. **Tính năng:** nhận diện đối tượng và giám sát chất lượng ảnh.

Đây là gói bài nộp được sắp xếp từ notebook, slide và kết quả đã cung cấp. Không chạy lại YOLO trong lần sắp xếp này. Đề lab được cung cấp thiếu phần Bước 4/T1–T8, nên mã T1 dựa trên notebook hiện có.

## Cấu trúc

```text
K4-Track4-Day04-TenNhom-Sensor-Reality-Sprint/
├── TEAMMATES.md
├── team.md                              # cùng danh sách và phân công
├── README.md
├── requirements.txt
├── notebooks/camera_health_lab_colab.ipynb
├── configs/benchmark.json
├── results/
│   ├── config.json
│   ├── summary.csv
│   ├── per_image_metrics.csv
│   ├── proxy_alerts.csv
│   ├── before_after.png
│   ├── metric_curves.png
│   ├── report_template.md
│   └── examples/                         # 25 ảnh minh họa
├── reports/
│   ├── group_report.md                   # 5 mục nội dung lab
│   ├── 2A202602813.md                    # Nguyễn Hữu Thành
│   ├── 2A202602723.md                    # Lê Anh Duy
│   ├── 2A202602791.md                    # Tống Trần Tiến Dũng
│   ├── 2A202602929.md                    # Vũ Quốc Huy
│   └── 2A202602473.md                    # Nguyễn Hoàng Cường
├── slides/camera_health_lab_final.pptx    # slide chung, giữ nguyên
└── docs/
    ├── METHOD_AND_REPRODUCIBILITY.md
    ├── SUBMISSION_CHECKLIST.md
    └── FILE_MANIFEST.csv
```

## Xem bằng chứng

- [Báo cáo nhóm](reports/group_report.md), [CSV tổng hợp](results/summary.csv), [CSV từng mẫu](results/per_image_metrics.csv).
- [Ảnh trước/sau](results/before_after.png), [biểu đồ metric](results/metric_curves.png), [slide chung](slides/camera_health_lab_final.pptx).
- [Cấu hình lần chạy đã cung cấp](configs/benchmark.json), [nguồn và cách tái hiện](docs/METHOD_AND_REPRODUCIBILITY.md).

## Cách chạy

Mở `notebooks/camera_health_lab_colab.ipynb` trong Google Colab. Chọn Runtime > Run all; T4 GPU nếu có, CPU cũng được. Notebook có cell `%pip` và tự tải ảnh bus.jpg, weights YOLOv8n. Cần internet cho lần tải đầu. Notebook gốc xuất kết quả vào `/content/camera_health_lab/results` rồi tải ZIP; giải nén kết quả mới vào `results/` và cập nhật `configs/benchmark.json` nếu chạy lại. Hướng dẫn này là đường chạy dự kiến từ notebook, chưa được chạy lại trong lần tổ chức file.

Để dùng các phiên bản ghi trong lần chạy đã có, thay cell `%pip` bằng:

```python
%pip install numpy==2.1.3 pandas==2.2.3 matplotlib==3.10.0 opencv-python-headless==5.0.0.93 ultralytics==8.4.173
```

Các version trên được chép từ config gốc, chưa kiểm tra khả năng cài lại. Notebook gốc dùng khoảng phiên bản Ultralytics nên lần chạy mới có thể khác. Chưa có hash weights, phiên bản Python/PyTorch hoặc thông tin thiết bị của lần chạy cũ để bảo đảm tái hiện bit-for-bit.

## Trước khi nộp

Đã điền đúng 5 họ tên/MSSV trong `TEAMMATES.md` và `team.md`, có 5 báo cáo riêng theo MSSV. Cần điền tên nhóm và URL repo chung. Khi đổi phân công, cập nhật cả hai file danh sách cho đồng nhất. Phân công là đề xuất, từng người điều chỉnh theo công việc thực tế rồi tự nộp báo cáo riêng cùng URL repo trên VLearn. Xem [checklist](docs/SUBMISSION_CHECKLIST.md).

## Đưa lên Git

Dùng chính thư mục này làm gốc repository, để `TEAMMATES.md`, `README.md`, `notebooks/`, `results/` nằm ngay ở gốc trên GitHub. Không push cả thư mục `day4` chứa các bản gốc và ZIP trùng lặp. Tên repo nên theo `K4-Track4-Day04-TenNhom-Sensor-Reality-Sprint`, thay `TenNhom` bằng tên thật. Đổi tên thư mục không ảnh hưởng các liên kết tương đối trong báo cáo.

Trong terminal tại thư mục gốc này, nếu chưa có Git:

```powershell
git init
git add .
git commit -m "Add Day 04 Sensor Reality Sprint lab"
git branch -M main
git remote add origin URL_REPO_CUA_NHOM
git push -u origin main
```

Thay `URL_REPO_CUA_NHOM` bằng URL repo đã tạo. Nếu đã có repository/remote, dùng cấu hình hiện tại, không chạy lại bước thêm remote. Sau khi push, điền URL vào các báo cáo và commit cập nhật.
