---
title: "Cấu hình runtime integration cho RDS, S3 và SES"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5.5. </b> "
---

## Mục tiêu

Cấp cho TechBlog các setting RDS, S3, SES, Region và log file cần trên EC2 mà không hard-code credential trong JAR hoặc repository.

## Điều kiện trước khi thực hiện

- `/home/ec2-user/techblog.jar` đã tồn tại.
- `techblog-ec2-role` đã gắn vào EC2.
- EC2 kết nối được RDS.
- S3 bucket và SES identity tồn tại trong `ap-southeast-1`.

## Các bước thực hiện

Tạo configuration directory được bảo vệ và application log directory:

```bash
sudo install -d -m 700 /etc/techblog
sudo install -d -o ec2-user -g ec2-user -m 750 /var/log/techblog
sudo touch /etc/techblog/techblog.env
sudo chmod 600 /etc/techblog/techblog.env
```

Chỉnh `/etc/techblog/techblog.env` bằng các giá trị khớp property binding của ứng dụng:

```bash
DB_URL=jdbc:mysql://<RDS_ENDPOINT>:3306/techblog
DB_USERNAME=admin
DB_PASSWORD=<DB_PASSWORD>
AWS_REGION=ap-southeast-1
TECHBLOG_STORAGE_PROVIDER=s3
TECHBLOG_S3_BUCKET=techblog-uploads-ttv-2026
TECHBLOG_PRESIGN_TTL_MINUTES=10
TECHBLOG_SES_ENABLED=true
TECHBLOG_SES_FROM_EMAIL=<SES_VERIFIED_SENDER_EMAIL>
TECHBLOG_SES_ADMIN_RECIPIENT=<SES_VERIFIED_ADMIN_EMAIL>
LOGGING_FILE_NAME=/var/log/techblog/application.log
```

Không đưa environment file thật vào report. Nếu source code dùng environment-variable name khác, dùng đúng tên trong configuration class hiện tại và cập nhật hai bản ngôn ngữ giống nhau.

## Cấu hình

| Biến | Mục đích | Secret |
|---|---|---|
| `DB_URL` | JDBC endpoint của RDS private | Không, nhưng không để endpoint thật trong screenshot |
| `DB_USERNAME` | RDS application/master user dùng trong demo | Không |
| `DB_PASSWORD` | RDS password | Có |
| `AWS_REGION` | SDK Region | Không |
| `TECHBLOG_STORAGE_PROVIDER` | Chọn S3 thay cho local provider mặc định | Không |
| `TECHBLOG_S3_BUCKET` | S3 object-storage bucket | Không |
| `TECHBLOG_PRESIGN_TTL_MINUTES` | Thời hạn presigned GET URL; giá trị `10` | Không |
| `TECHBLOG_SES_ENABLED` | Bật SES notification component | Không |
| `TECHBLOG_SES_FROM_EMAIL` | Verified SES sender | Dữ liệu identity; cần che |
| `TECHBLOG_SES_ADMIN_RECIPIENT` | Verified Sandbox admin recipient | Dữ liệu identity; cần che |
| `LOGGING_FILE_NAME` | Spring Boot application log file | Không |

AWS SDK for Java cần dùng default credential provider chain. Không thêm `AWS_ACCESS_KEY_ID` hoặc `AWS_SECRET_ACCESS_KEY`.

## Hành vi application integration

- Avatar marker dùng `s3:avatars/<uuid>.<ext>`; marker ảnh bài viết dùng `s3:posts/<uuid>.<ext>`.
- MySQL lưu marker, không lưu binary ảnh hoặc presigned URL; reference ảnh local legacy vẫn được hỗ trợ.
- Khi thay hoặc xóa managed media, ứng dụng cleanup S3 object tương ứng.
- Ảnh private được render bằng presigned GET URL hết hạn sau 10 phút.
- Sau khi registration được lưu, SES gửi admin notification chỉ gồm username, display name nếu có, registration email và registration time. Lỗi gửi mail không được rollback user mới.
- Application logging ghi event INFO/WARN/ERROR phù hợp vào `/var/log/techblog/application.log`, không chứa password, token, cookie hoặc nội dung nhạy cảm.

## Cách kiểm tra

Kiểm tra permission mà không in nội dung file:

```bash
sudo stat -c '%a %U:%G %n' /etc/techblog/techblog.env
sudo ls -ld /var/log/techblog
```

Environment file cần có mode `600`. Runtime integration được kiểm tra đầy đủ trong module 5.6 bằng object, email và log test.

## Kết quả mong đợi

EC2 runtime có đủ integration value, service user ghi được log path và không có static AWS credential.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Spring property rỗng | So sánh environment name với `application.properties` hoặc configuration class hiện tại. |
| S3/SES client báo thiếu Region | Xác nhận `AWS_REGION=ap-southeast-1` và restart service. |
| Upload vẫn ghi local | Xác nhận `TECHBLOG_STORAGE_PROVIDER=s3` có trong environment file đang active. |
| SES component không active | Xác nhận `TECHBLOG_SES_ENABLED=true` và hai giá trị email đã verify được cấu hình. |
| Ứng dụng không ghi được log file | Kiểm tra owner và permission của `/var/log/techblog`. |
| Secret xuất hiện trong shell history | Tránh inline export có password thật; chỉnh restricted file cẩn thận. |

## Ghi chú chi phí và bảo mật

Environment file phù hợp cho learning demo khi chỉ root đọc được. AWS Secrets Manager hoặc Parameter Store vẫn là hướng phát triển.

<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/runtime-config-redacted.png. Caption: *Hình 5.5.5.1. Runtime variable name đã che giá trị và protected file permission.* -->
