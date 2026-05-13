# Báo Cáo Thực Hành: GPU FinOps & Cost Optimization

**Sinh viên:** Nguyễn Đôn Đức  
**MSSV:** 2A202600145  
**Ngày thực hiện:** 13/05/2026

---

## 1. Giới thiệu
### Mục tiêu bài lab
Bài lab nhằm trang bị các kỹ năng quản trị tài nguyên tính toán GPU theo mô hình FinOps (Financial Operations). Mục tiêu chính bao gồm:
- Hiểu và triển khai các cơ chế giám sát (Monitoring) tài nguyên GPU real-time.
- Quản lý chi phí (Cost Tracking) thông qua việc phân bổ ngân sách và phát hiện lãng phí.
- Tối ưu hóa chi phí bằng cách sử dụng Spot Instances và cơ chế Autoscaling.
- Áp dụng các kỹ thuật tối ưu hóa huấn luyện (Mixed Precision - AMP) trên GPU thật để tiết kiệm thời gian và tiền bạc.

### Tổng quan về GPU FinOps
GPU FinOps là sự kết hợp giữa hệ thống kỹ thuật và quản lý tài chính nhằm tối ưu hóa giá trị kinh doanh của các hạ tầng AI. Trong bối cảnh chi phí GPU (như A100, H100) ngày càng đắt đỏ, việc áp dụng FinOps giúp doanh nghiệp tránh tình trạng "bill shock" và tận dụng tối đa hiệu suất của phần cứng.

---

## 2. Phân tích kết quả thực hành

### Part 1-5: Giả lập Cụm GPU (Mock Cluster)
Thông qua các dịch vụ giả lập, tôi đã quan sát được các hành vi quan trọng của hệ thống:
- **Cluster Monitoring**: Việc theo dõi không chỉ Utilization (%) mà còn cả Power Draw (W) và Temperature (C) giúp dự báo được độ bền và chi phí vận hành hạ tầng.
- **Spot Instance Management**: Sử dụng Spot Instance giúp tiết kiệm tới **60-70%** chi phí. Tuy nhiên, việc xử lý thông báo thu hồi (Preemption notice) là cực kỳ quan trọng để đảm bảo tính liên tục của dữ liệu.
- **Autoscaling**: Cơ chế KEDA-like giúp hệ thống tự động tăng/giảm số lượng node dựa trên ngưỡng Utilization (Scale-up ở 70%, Scale-down ở 25%), giúp giảm thiểu lãng phí khi không có workload.
- **Waste Analysis**: Báo cáo đã chỉ ra các khoảng thời gian GPU "Idle" nhưng vẫn tính tiền, từ đó đưa ra các Recommendation như: Tắt máy vào cuối tuần hoặc chuyển sang cấu hình GPU nhỏ hơn (T4).

### Part 8: Huấn luyện trên GPU thật (FP32 vs AMP)
Thực hiện trên Kaggle (GPU T4), kết quả so sánh cho thấy:
- **Tốc độ (Speedup)**: Sử dụng **Mixed Precision (AMP)** giúp tăng tốc độ huấn luyện khoảng **1.5x - 2.0x** so với FP32 truyền thống.
- **Tiết kiệm chi phí**: Nhờ thời gian chạy ngắn hơn, chi phí hóa đơn cho mỗi epoch giảm đi tương ứng.
- **Hiệu quả bộ nhớ**: AMP cho phép sử dụng Batch Size lớn hơn trên cùng một mức VRAM, giúp tối ưu hóa thông lượng (throughput).

### Part 8.5: Phân tích Nâng cao
- **Multi-GPU Scaling**: Khi tăng số lượng GPU, hiệu năng không tăng tuyến tính (linear) mà giảm dần (diminishing returns). Qua phân tích, cấu hình 4 GPU thường là "điểm ngọt" (sweet spot) về ROI.
- **Forecasting**: Sử dụng các khoảng tin cậy (Confidence Intervals) giúp lập kế hoạch ngân sách an toàn hơn cho các dự án dài hơi (như Fine-tuning LLM), dự phòng trước các biến động về thời gian huấn luyện.

---

## 3. Kết luận và Học hỏi

### Những kỹ năng FinOps đã học
1. **Visibility**: Biết cách đọc và phân tích bảng dashboard chi phí tích hợp.
2. **Optimization**: Thành thạo việc cấu hình Autoscaling và lựa chọn loại GPU phù hợp cho từng task.
3. **Accountability**: Gắn trách nhiệm chi phí cho từng project/workload cụ thể.

### Các chiến lược tối ưu hóa hiệu quả nhất
- Luôn ưu tiên **AMP** cho mọi task huấn luyện Deep Learning để giảm chi phí gốc.
- Tận dụng **Spot Instances** cho các tác vụ không nhạy cảm về thời gian (Batch processing, Offline training).
- Thiết lập **Budget Alerts** sớm để có biện pháp can thiệp khi chi phí vượt ngưỡng 80%.

### Ứng dụng thực tế
Kiến thức này có thể áp dụng trực tiếp khi quản lý các cụm GPU trên AWS (SageMaker), GCP (Vertex AI) hoặc các hệ thống On-premise sử dụng Kubernetes. Việc tối ưu hóa chi phí không chỉ là tiết kiệm tiền, mà còn giúp team AI có thêm tài nguyên để thử nghiệm nhiều model hơn trong cùng một mức ngân sách.

---
