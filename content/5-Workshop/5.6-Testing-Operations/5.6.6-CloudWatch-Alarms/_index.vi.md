---
title: "Tạo và kiểm tra CloudWatch CPU Alarm"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.6.6. </b> "
---

## Mục tiêu

Tạo và kiểm tra CloudWatch alarm duy nhất đã triển khai cho TechBlog: `techblog-high-cpu`. Workshop không tuyên bố có status-check alarm hoặc SNS notification action.

## Điều kiện trước khi thực hiện

- `techblog-ec2` đang running trong `ap-southeast-1`.
- Standard EC2 metric `CPUUtilization` hiển thị trong CloudWatch.
- Nhóm biết instance ID nhưng sẽ che trong screenshot.

## Các bước thực hiện

1. Mở **Amazon CloudWatch → Alarms → All alarms**.
2. Chọn **Create alarm → Select metric**.
3. Chọn **EC2 → Per-Instance Metrics → CPUUtilization** của `techblog-ec2`.
4. Cấu hình:
   - Statistic: **Average**
   - Period: **5 minutes**
   - Threshold type: **Static**
   - Condition: **Greater than 70%**
   - Datapoints to alarm: **2 out of 2**, tương đương 10 phút
   - Missing data: **Treat missing data as good/not breaching**
5. Để trống notification action vì Amazon SNS chưa được triển khai.
6. Đặt alarm name `techblog-high-cpu` và tạo alarm.

## Cấu hình

| Thiết lập | Giá trị đã xác minh |
|---|---|
| Alarm | `techblog-high-cpu` |
| Metric | EC2 `CPUUtilization` |
| Statistic | Average |
| Threshold | Lớn hơn 70% |
| Period | 5 phút |
| Evaluation | 2 trong 2 datapoint, tổng cộng 10 phút |
| Missing data | Good / not breaching |
| Notification action | Không có |

## Cách kiểm tra

1. Mở alarm, so sánh name, metric, EC2 dimension, statistic, threshold, period, evaluation count và missing-data setting.
2. Xác nhận alarm có metric data gần đây.
3. Ghi current state đúng như console; không tự tạo trạng thái `OK` hoặc `ALARM`.
4. Không tạo artificial load chỉ để ép alarm đổi state.
5. Xác nhận không có nội dung nói CloudWatch gửi email trực tiếp bằng SES hoặc đã cấu hình SNS.

## Kết quả mong đợi

`techblog-high-cpu` theo dõi CPU cao kéo dài của đúng EC2 instance. Nhóm xem trạng thái trong CloudWatch Console vì alarm không có notification action.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Alarm hiển thị Insufficient data | Kiểm tra đúng instance/Region và chờ hai period 5 phút. |
| Sai instance dimension | Sửa hoặc tạo lại alarm với `techblog-ec2`. |
| Evaluation không phải 2/2 | Đặt hai evaluation period và hai datapoint to alarm. |
| Không nhận email | Đây là trạng thái dự kiến; chưa cấu hình SNS notification action. |

## Ghi chú chi phí và bảo mật

Chỉ giữ alarm hữu ích đã xác minh và xóa khi cleanup. Không để lộ EC2 instance ID trong screenshot.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/cloudwatch-alarm-cpu.png. Caption: *Hình 5.6.6.1. Metric, threshold 70%, period 5 phút và evaluation 2/2 của techblog-high-cpu sau khi che thông tin.* -->
