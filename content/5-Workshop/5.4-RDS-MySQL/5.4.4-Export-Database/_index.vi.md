---
title: "Export database TechBlog ở local"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

## Mục tiêu

Tạo file dump `techblog.sql` ở local trước khi launch EC2. Việc upload và import được chuyển sang 5.5.3.

## Điều kiện trước khi thực hiện

- MySQL/Laragon đang chạy ở local.
- Database local `techblog` chứa dữ liệu ứng dụng đã kiểm tra.
- Vị trí output nằm ngoài Hugo repository.

## Các bước thực hiện

1. Mở PowerShell trên máy Windows local.
2. Export database:

```powershell
mysqldump -u root techblog > C:\WORKING\techblog.sql
```

3. Xác nhận file tồn tại và không rỗng:

```powershell
Get-Item C:\WORKING\techblog.sql
```

4. Giữ dump file ngoài Git và public report.
5. Chưa upload file hoặc chạy import RDS cho đến khi có EC2 ở 5.5.3.

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| Nguồn | Database MySQL/Laragon local `techblog` |
| Dump file | `C:\WORKING\techblog.sql` |
| Đích ở bước sau | RDS database `techblog` |
| Thao tác hiện tại | Chỉ export local |

## Cách kiểm tra

Kiểm tra dung lượng file và nếu cần chỉ xem schema/table header ở local. Không đưa row chứa dữ liệu user, password hash, email hoặc thông tin cá nhân khác vào workshop.

## Kết quả mong đợi

`C:\WORKING\techblog.sql` sẵn sàng để chuyển an toàn sau khi tạo EC2. Không có command nào trong module 5.4 phụ thuộc EC2 instance.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Không tìm thấy `mysqldump` | Chạy từ thư mục binary của MySQL/Laragon hoặc thêm thư mục đó vào `PATH`. |
| Local báo Access denied | Dùng đúng MySQL account local và không ghi password vào report. |
| Dump file rỗng | Xác nhận database `techblog` tồn tại rồi export lại. |

## Ghi chú chi phí và bảo mật

Export local không phát sinh AWS charge. Xem SQL dump là dữ liệu nhạy cảm, không commit hoặc upload lên report website.

<!-- TODO: Thêm /images/5-Workshop/5.4-RDS-MySQL/local-database-export.png. Caption: *Hình 5.4.4.1. File techblog.sql được export và kiểm tra ở local trước khi tạo EC2.* -->
