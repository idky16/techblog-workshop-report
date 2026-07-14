---
title: "Ghi nhận cấu hình kết nối RDS"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Mục tiêu

Ghi các giá trị RDS cần cho bước EC2 và Spring Boot sau này mà không lộ endpoint/password thật, đồng thời chưa thử kết nối từ EC2 khi EC2 chưa tồn tại.

## Điều kiện trước khi thực hiện

- `techblog-db` ở trạng thái **Available** trong `ap-southeast-1`.
- Đã cấu hình database `techblog` và port `3306`.
- RDS Security Group tham chiếu `<EC2_SECURITY_GROUP_ID>`.

## Các bước thực hiện

1. Mở **Amazon RDS → Databases → techblog-db**.
2. Trong **Connectivity & security**, ghi endpoint ở nơi không public.
3. Trong workshop và screenshot, thay endpoint bằng `<RDS_ENDPOINT>`.
4. Ghi database `techblog`, port `3306` và username `admin`.
5. Giữ password thật trong deployment workflow được bảo vệ và luôn dùng `<DB_PASSWORD>` trong tài liệu.
6. Không chạy `ssh`, `mysql`, `scp` hoặc lệnh import trong trang này.

## Cấu hình

| Hạng mục | Giá trị an toàn trong report |
|---|---|
| Host | `<RDS_ENDPOINT>` |
| Port | `3306` |
| Database | `techblog` |
| Username | `admin` |
| Password | `<DB_PASSWORD>` |
| Public access | No |

JDBC value dùng ở bước sau được ghi an toàn:

```text
jdbc:mysql://<RDS_ENDPOINT>:3306/techblog
```

## Cách kiểm tra

Xác nhận các giá trị trên RDS console và Security Group đang chọn là `<RDS_SECURITY_GROUP_ID>`. Kết nối thật được kiểm tra từ EC2 ở 5.5.3.

## Kết quả mong đợi

Nhóm có checklist giá trị kết nối đầy đủ, đã che thông tin để dùng ở deployment stage. Chưa có EC2 runtime action nào được thực hiện sớm.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Copy sai endpoint | Copy lại từ **Connectivity & security**, sau đó chỉ giữ `<RDS_ENDPOINT>` trong report. |
| Password xuất hiện trong ghi chú/screenshot | Xóa hoặc che ngay và dùng `<DB_PASSWORD>`. |
| Chưa test được kết nối | Đây là trạng thái đúng khi chưa launch EC2; tiếp tục bước export local. |

## Ghi chú chi phí và bảo mật

Đọc configuration không phát sinh chi phí bổ sung. Không public endpoint đầy đủ, password, AWS Account ID hoặc Security Group ID.

<!-- TODO: Thêm /images/5-Workshop/5.4-RDS-MySQL/rds-connection-settings.png. Caption: *Hình 5.4.3.1. Cấu hình kết nối RDS đã che thông tin, được ghi trước khi tạo EC2.* -->
