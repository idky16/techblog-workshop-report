---
title: "Tạo RDS MySQL"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Mục tiêu

Tạo Amazon RDS for MySQL private cho TechBlog.

## Điều kiện trước khi thực hiện

- Region là `ap-southeast-1`.
- Nhóm đã thống nhất database name là `techblog`.

## Các bước thực hiện

1. Mở **Amazon RDS**.
2. Tạo MySQL database.
3. Đặt DB identifier là `techblog-db`.
4. Chọn instance class `db.t3.micro`.
5. Dùng Single-AZ cho demo.
6. Đặt **Publicly accessible** là **No**.
7. Đặt database name là `techblog`.
8. Dùng port `3306`.
9. Đặt master user là `admin`.
10. Chờ status thành **Available**.

## Cấu hình

| Thiết lập | Giá trị |
|---|---|
| DB identifier | `techblog-db` |
| Engine | MySQL |
| Instance class | `db.t3.micro` |
| Availability | Single-AZ |
| Public access | No |
| Database name | `techblog` |
| Port | `3306` |
| Master user | `admin` |

## Cách kiểm tra

Ghi endpoint trong report bằng placeholder `<RDS_ENDPOINT>`. Không dán endpoint đầy đủ nếu chứa thông tin đặc thù tài khoản.

## Kết quả mong đợi

RDS đạt trạng thái **Available** và sẵn sàng cấu hình Security Group.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| RDS tạo lâu | Chờ đến khi status là **Available**. |
| Sai Region | Kiểm tra lại `ap-southeast-1`. |

## Ghi chú chi phí và bảo mật

Single-AZ và `db.t3.micro` giúp giảm chi phí demo. Giữ public access disabled.

<!-- TODO: Thêm /images/5-Workshop/5.4-RDS-MySQL/rds-available.png. Caption: *Hình 5.4.1.1. techblog-db ở trạng thái Available và tắt public access.* -->
