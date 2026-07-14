---
title: "Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

## Thời gian

29/06/2026 - 05/07/2026

## Mục tiêu trong tuần

- Kiểm thử end-to-end các role và AWS integration.
- Review security boundary, chuyển lỗi triển khai thành hướng dẫn troubleshooting.

## Công việc đã thực hiện

- Kiểm thử lại Guest, USER, EDITOR và ADMIN với RDS database đã deploy.
- Xác minh S3 avatar/ảnh bài viết có thể tạo, hiển thị, thay, xóa và vẫn tương thích ảnh local legacy.
- Kiểm tra Amazon SES registration notification, CloudWatch log event, `techblog-high-cpu`, `systemd`, port `8080` và Java process.
- Review SSH, EC2/RDS Security Group, S3 Block Public Access, IAM Role credential, protected environment file và SES Sandbox.
- Tổng hợp lỗi đã gặp: public IP thay đổi, PEM permission, endpoint/password sai, connection failure, Java command bị tách, port `8080` đóng, kiểm tra quá sớm và HTTP 413.

## Kết quả và bài học

- Hoàn thành regression checklist thực tế cho live demo.
- Xác nhận security boundary hiện tại và ghi rõ giới hạn HTTP, public IP thay đổi, Single-AZ, CPU alarm chỉ theo dõi trên console.
- Biết cách tách riêng service, port, process, database, object, email và log layer khi troubleshooting.
