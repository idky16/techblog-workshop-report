---
title: "Tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

## Thời gian

01/06/2026 - 07/06/2026

## Mục tiêu trong tuần

- Tạo EC2 runtime và hoàn tất database migration.
- Giữ TechBlog hoạt động độc lập với phiên SSH tương tác.

## Công việc đã thực hiện

- Launch `techblog-ec2` bằng Amazon Linux 2023, `t3.micro`, 8 GiB gp3, Security Group đã chuẩn bị và `techblog-ec2-role`.
- Giới hạn SSH theo IP cá nhân hiện tại, mở TCP `8080` cho demo và cài Java 17 Amazon Corretto cùng MariaDB client.
- Upload `techblog.sql`, kết nối RDS, import schema và xác minh chín bảng TechBlog.
- Build/upload `techblog-0.0.1-SNAPSHOT.jar`, cấu hình `/etc/techblog/techblog.env` và chạy application bằng `techblog.service`.

## Kết quả và bài học

- Xác minh trạng thái `active (running)` và HTTP 200 bằng `curl -I http://localhost:8080`.
- Website tiếp tục chạy sau khi đóng phiên SSH.
- Xử lý các lỗi về SSH allow-list, permission của PEM, RDS endpoint sai, database password sai và port `8080` chưa mở.
