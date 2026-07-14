---
title: "Cấu hình Security Group EC2-RDS"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Mục tiêu

Chuẩn bị Security Group reference từ EC2 đến RDS trước khi launch EC2, không public RDS ra internet.

## Điều kiện trước khi thực hiện

- RDS đã available và có thể chỉnh Security Group.
- Chưa cần EC2 instance tồn tại.

## Các bước thực hiện

1. Trong VPC/EC2 console, tạo application Security Group sẽ gắn cho `techblog-ec2` và ghi bằng `<EC2_SECURITY_GROUP_ID>`.
2. Mở hoặc tạo RDS Security Group và ghi bằng `<RDS_SECURITY_GROUP_ID>`.
3. Thêm inbound rule cho RDS:
   - Type: MySQL/Aurora
   - Port: `3306`
   - Source: `<EC2_SECURITY_GROUP_ID>`
4. Gắn `<RDS_SECURITY_GROUP_ID>` vào `techblog-db`.
5. Không dùng `0.0.0.0/0` cho RDS. Rule SSH và application port được hoàn thiện khi launch EC2 ở 5.5.1.

## Cấu hình

| Rule | Giá trị |
|---|---|
| Protocol | TCP |
| Port | `3306` |
| Source | `<EC2_SECURITY_GROUP_ID>` |
| Public source | Không cho phép |

## Cách kiểm tra

Kiểm tra trên console rằng inbound rule của RDS tham chiếu `<EC2_SECURITY_GROUP_ID>`, không phải public CIDR. Chưa thể test network connection cho đến 5.5.3.

## Kết quả mong đợi

Ranh giới Security Group đã sẵn sàng. Khi launch `techblog-ec2` ở 5.5.1, việc gắn group đã chuẩn bị sẽ cho instance đủ điều kiện truy cập RDS port `3306`.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Không thấy EC2 instance | Đây là trạng thái đúng; bước này tham chiếu Security Group đã chuẩn bị, không tham chiếu instance. |
| Chọn sai source type | Chọn Security Group ID thay vì CIDR. |

## Ghi chú chi phí và bảo mật

Security Group reference an toàn hơn mở database ra internet.

<!-- TODO: Thêm /images/5-Workshop/5.4-RDS-MySQL/rds-security-group.png. Caption: *Hình 5.4.2.1. RDS port 3306 chỉ nhận EC2 Security Group.* -->
