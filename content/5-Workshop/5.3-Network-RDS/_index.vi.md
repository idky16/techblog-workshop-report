---
title: "Chuẩn bị mạng và Amazon RDS"
date: 2026-07-10
weight: 3
pre: " <b> 5.3. </b> "
---

## Tạo Security Group

1. Tạo Security Group `techblog-ec2-sg` cho máy chủ ứng dụng.
2. Trong giai đoạn kiểm tra, cho phép TCP 8080 chỉ từ địa chỉ IP quản trị. SSH 22 cũng chỉ mở từ IP quản trị nếu không dùng Systems Manager.
3. Tạo `techblog-rds-sg` cho database.
4. Thêm inbound MySQL/Aurora TCP 3306 vào `techblog-rds-sg`, nhưng **source phải là `techblog-ec2-sg`**, không dùng `0.0.0.0/0`.

## Tạo RDS for MySQL

1. Mở Amazon RDS và chọn **Create database**.
2. Chọn MySQL, cấu hình Free Tier hoặc Dev/Test nhỏ, Single-AZ và storage giới hạn.
3. Đặt database ban đầu là `techblog` và tắt Public access nếu EC2 cùng VPC.
4. Gắn `techblog-rds-sg`, bật backup phù hợp thời gian workshop và ghi lại endpoint.
5. Lưu username/password trong Secrets Manager hoặc SecureString của Parameter Store.

Chuỗi JDBC có dạng:

```text
jdbc:mysql://<rds-endpoint>:3306/techblog?useSSL=true&serverTimezone=UTC
```

## Xác minh kết nối

Sau khi EC2 được tạo, kiểm tra DNS endpoint và kết nối cổng 3306 từ EC2. Nếu timeout, kiểm tra VPC, subnet, route table, Network ACL và quan hệ giữa hai Security Group. Không mở RDS public chỉ để xử lý lỗi kết nối.

Với bản demo, `spring.jpa.hibernate.ddl-auto=update` có thể dùng để khởi tạo schema. Môi trường lâu dài nên chuyển sang Flyway hoặc Liquibase.
