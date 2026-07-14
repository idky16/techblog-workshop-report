---
title: "Tạo EC2 và cấu hình Security Group"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Mục tiêu

Launch EC2 instance dùng để chạy Spring Boot JAR của TechBlog.

## Điều kiện trước khi thực hiện

- IAM Role `techblog-ec2-role` đã tồn tại.
- Application Security Group và RDS reference đã được chuẩn bị ở 5.4.2.

## Các bước thực hiện

1. Mở **Amazon EC2**.
2. Launch instance tên `techblog-ec2`.
3. Chọn Amazon Linux 2023.
4. Chọn instance type `t3.micro`.
5. Dùng 8 GiB gp3 storage.
6. Bật auto-assign public IP cho demo.
7. Gắn IAM instance profile `techblog-ec2-role`.
8. Gắn application Security Group đã chuẩn bị ở 5.4.2 và xác nhận:
   - SSH `22` từ `<MY_PUBLIC_IP>`.
   - TCP `8080` public cho demo.
   - Không cần HTTP `80` vì chưa triển khai Nginx.
9. Không mở MySQL trên EC2.

## Cấu hình

| Thiết lập | Giá trị |
|---|---|
| Name | `techblog-ec2` |
| AMI | Amazon Linux 2023 |
| Instance type | `t3.micro` |
| Storage | 8 GiB gp3 |
| IAM role | `techblog-ec2-role` |
| App port | `8080` |

## Cách kiểm tra

Xác nhận instance đang running, dùng Security Group đã chuẩn bị và `techblog-ec2-role` hiển thị trong instance details. Đây là lần đầu role được gắn vào EC2 trong workshop.

## Kết quả mong đợi

EC2 sẵn sàng để SSH và cài Java.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| SSH timeout | Kiểm tra public IP và inbound rule cho `<MY_PUBLIC_IP>`. |
| Port 8080 không truy cập được sau này | Thêm inbound rule TCP `8080` cho demo access. |

## Ghi chú chi phí và bảo mật

Port `8080` chỉ public cho demo và chấm bài. Sau demo nên đóng hoặc giới hạn lại.

<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/ec2-running.png. Caption: *Hình 5.5.1.1. techblog-ec2 đang running với techblog-ec2-role.* -->
<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/ec2-security-group.png. Caption: *Hình 5.5.1.2. Inbound rule demo với SSH giới hạn và TCP 8080 public.* -->
