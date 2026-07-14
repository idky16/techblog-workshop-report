---
title: "Tuần 4"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

## Thời gian

11/05/2026 - 17/05/2026

## Mục tiêu trong tuần

- Thiết kế kiến trúc AWS phù hợp với Spring Boot MVC monolith.
- Giữ bản demo đầu đơn giản, an toàn và tiết kiệm chi phí.

## Công việc đã thực hiện

- Chọn `ap-southeast-1` làm Region triển khai.
- Thiết kế luồng chính: browser → Internet Gateway → EC2 public port `8080` → RDS MySQL private port `3306`.
- Bổ sung S3 private, EC2 IAM Role, Amazon SES, CloudWatch Logs, một CPU alarm và AWS Budgets vào workflow dự án.
- So sánh chi phí và độ phức tạp, sau đó giới hạn kiến trúc demo ở các tài nguyên AWS cần thiết cho TechBlog.

## Kết quả và bài học

- Hoàn thành workflow diagram phản ánh đúng component và integration boundary đã triển khai.
- Chọn cấu hình EC2/RDS nhỏ và Single-AZ cho môi trường học tập.
- Hiểu cách tách kiến trúc đã triển khai với hướng cải tiến tương lai trong tài liệu kỹ thuật.
