---
title: "Kết nối RDS và import database"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

## Mục tiêu

Sau khi EC2 tồn tại, kiểm tra đường private EC2–RDS và import file dump `techblog.sql` từ local vào database `techblog`.

## Điều kiện trước khi thực hiện

- `techblog-ec2` đang chạy với application Security Group đã chuẩn bị ở 5.4.2.
- RDS ở trạng thái **Available** và port `3306` nhận Security Group của EC2.
- MariaDB client đã cài trên EC2.
- `C:\WORKING\techblog.sql` đã export ở 5.4.4.

## Các bước thực hiện

### 1. Upload SQL dump lên EC2

Chạy từ PowerShell local:

```powershell
scp -i "C:\WORKING\techblog-key.pem" "C:\WORKING\techblog.sql" ec2-user@<EC2_PUBLIC_IP>:/home/ec2-user/
```

### 2. Kiểm tra kết nối RDS từ EC2

```bash
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p
```

Sau đó chạy:

```sql
SHOW DATABASES;
USE techblog;
SHOW TABLES;
```

Thoát client trước khi import nếu cần.

### 3. Import và kiểm tra database

```bash
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p techblog < /home/ec2-user/techblog.sql
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p techblog
```

```sql
SHOW TABLES;
```

Schema đã xác minh có chín bảng:

`categories`, `comment_reports`, `comments`, `editor_upgrade_requests`, `post_likes`, `posts`, `saved_collections`, `saved_posts`, `users`.

Sau khi kiểm tra, xóa dump khỏi EC2 nếu không còn cần:

```bash
rm /home/ec2-user/techblog.sql
```

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| Client host | `techblog-ec2` |
| Database host | `<RDS_ENDPOINT>` |
| Port | `3306` |
| Database | `techblog` |
| Import file | `/home/ec2-user/techblog.sql` |

## Cách kiểm tra

Xác nhận client kết nối qua Security Group reference và `SHOW TABLES` trả chín bảng. Không để lộ endpoint, password, row data hoặc thông tin cá nhân trong screenshot.

## Kết quả mong đợi

RDS private chứa schema/data TechBlog đã migrate và sẵn sàng cho runtime configuration của Spring Boot ở 5.5.5.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Unknown MySQL server host | Copy lại `<RDS_ENDPOINT>` và kiểm tra từng ký tự. |
| Access denied | Nhập lại đúng password, không ghi vào report hoặc command history. |
| Connection timeout | Xác nhận RDS available và inbound source là EC2 Security Group. |
| Import fail | Xác nhận database `techblog` tồn tại và đúng đường dẫn dump. |

## Ghi chú chi phí và bảo mật

Không mở RDS cho `0.0.0.0/0`. Xóa SQL dump trên EC2 sau khi kiểm tra thành công nếu không cần giữ để recovery.

<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/rds-import-verification.png. Caption: *Hình 5.5.3.1. Kết nối EC2–RDS và kết quả import chín bảng đã che thông tin.* -->
