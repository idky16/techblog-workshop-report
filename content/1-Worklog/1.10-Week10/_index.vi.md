---
title: "Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

## Thời gian

22/06/2026 - 28/06/2026

## Mục tiêu trong tuần

- Tập trung application log phù hợp và tạo tín hiệu theo dõi EC2 đơn giản.
- Giữ monitoring hữu ích mà không thu dữ liệu nhạy cảm hoặc thêm dịch vụ thừa.

## Công việc đã thực hiện

- Cấu hình Spring Boot ghi application event vào `/var/log/techblog/application.log`.
- Cài và cấu hình CloudWatch Agent gửi log đến Standard-class Log Group `/techblog/application`, retention 14 ngày.
- Kiểm tra log stream theo EC2 instance ID và event mới sau khi truy cập website.
- Tạo `techblog-high-cpu` với Average `CPUUtilization > 70%`, period 5 phút, 2/2 datapoint và missing data là not breaching.

## Kết quả và bài học

- Tập trung INFO, WARN và ERROR application event trong CloudWatch Logs.
- Xác nhận không log password, token, cookie hoặc request data nhạy cảm.
- Ghi nhận CPU alarm chưa có notification action và được theo dõi trực tiếp trên CloudWatch console.
