---
title: "Tuần 6"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Thời gian

25/05/2026 - 31/05/2026

## Mục tiêu trong tuần

- Thay MySQL local bằng managed database private.
- Chuẩn bị dữ liệu migration và ranh giới EC2–RDS theo đúng thứ tự.

## Công việc đã thực hiện

- Tạo `techblog-db` bằng Amazon RDS for MySQL, `db.t3.micro`, Single-AZ, database `techblog` và tắt public access.
- Chuẩn bị EC2 Security Group, cấu hình RDS Security Group chỉ nhận MySQL `3306` từ group này.
- Ghi endpoint, database, port và username bằng placeholder an toàn trong report.
- Export database local thành `techblog.sql` bằng `mysqldump` và kiểm tra dump trước khi tạo EC2.

## Kết quả và bài học

- Tạo được database path private mà không mở MySQL cho `0.0.0.0/0`.
- Chuẩn bị migration file có thể lặp lại, đồng thời không đưa password và endpoint đầy đủ vào tài liệu.
- Hiểu vì sao phải chờ EC2 tồn tại mới thực hiện network test và import.
