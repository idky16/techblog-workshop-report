---
title: "Chuẩn bị"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Mục tiêu

Chuẩn bị TechBlog baseline ở local, AWS account, cách truy cập an toàn và các giá trị triển khai trước khi tạo tài nguyên. Hoàn thành trang này giúp nhóm không phải debug application code, database migration và AWS permission cùng lúc.

## AWS account và Region

- Dùng một shared AWS account cho demo của nhóm TTV.
- Chọn **Asia Pacific (Singapore) – `ap-southeast-1`** cho EC2, RDS, S3, SES và CloudWatch.
- Không chia sẻ root account. Root chỉ dùng cho billing hoặc account recovery.
- Dùng IAM identity chỉ có permission cần thiết cho nhiệm vụ được giao.
- Tạo AWS Budgets trước EC2, RDS hoặc tài nguyên có tính phí khác.
- Xác nhận Amazon SES email identity đã Verified và account vẫn ở Sandbox tại `ap-southeast-1`.

## Công cụ local

Máy Windows phát triển cần Java 17, Maven Wrapper có sẵn trong repository TechBlog, Git/GitHub access, MySQL/Laragon, OpenSSH và trình duyệt.

```powershell
java -version
.mvnw.cmd --version
git --version
ssh -V
```

Kết quả kiểm tra:

| Công cụ | Kết quả mong đợi |
|---|---|
| Java | Version 17 |
| Maven | Wrapper chạy mà không cần cài Maven global |
| Git | Có thể đọc và cập nhật repository |
| MySQL/Laragon | Database local `techblog` đang sẵn sàng |
| OpenSSH | Có lệnh `ssh` và `scp` |

## Kiểm tra project và cấu hình AWS SDK

Xác nhận TechBlog vẫn là một Spring Boot MVC project và build sinh ra:

```text
target/techblog-0.0.1-SNAPSHOT.jar
```

Kiểm tra `pom.xml` và application configuration để xác định AWS SDK for Java module dùng cho S3 và SES. Không tự ghi dependency version trong report; dùng đúng version có trong repository. Xác nhận code lấy credential từ default AWS credential provider chain để EC2 sử dụng IAM Role.

Application configuration cần hỗ trợ:

- Region `ap-southeast-1`.
- Storage provider là `s3` trên EC2; local vẫn là mặc định cho môi trường phát triển.
- S3 bucket `techblog-uploads-ttv-2026`.
- S3 presigned GET URL có thời hạn 10 phút.
- SES sender `<SES_VERIFIED_SENDER_EMAIL>`.
- SES admin recipient `<SES_VERIFIED_ADMIN_EMAIL>` và enabled flag.
- RDS URL, username và password qua environment value.
- Application log file `/var/log/techblog/application.log`.
- Không hard-code Access Key, Secret Access Key, RDS password, endpoint hoặc email cá nhân.

## Kiểm tra database local và upload baseline

Trước khi migration, xác nhận MySQL/Laragon local có database `techblog` và các bảng dự kiến. Kiểm thử upload local ban đầu để làm baseline cho S3:

- Upload và thay avatar.
- Upload và thay ảnh bài viết.
- Database field/mapping dùng cho local-path baseline và S3 object key/reference.
- Hành vi delete khi user hoặc post đổi ảnh.

Không xóa local folder cho đến khi S3 path đã qua kiểm thử create/read/replace/delete.

## Build local

Build ứng dụng từ project root:

```powershell
.mvnw.cmd clean package -DskipTests
```

Sau đó kiểm tra artifact:

```powershell
Get-Item .\target\techblog-0.0.1-SNAPSHOT.jar
```

## Kiểm thử local

Chạy TechBlog tại `http://localhost:8080` và ghi nhận kết quả:

- Sign up, sign in và sign out.
- USER/Reader: đọc, like, save, comment, reply, report, profile, avatar, Writer request.
- EDITOR/Writer: tạo, sửa, xóa bài của mình và xem moderation status.
- ADMIN: approve/reject post và Writer request; quản lý user, category, comment, report.
- Đối chiếu giới hạn upload từ source: 50 MB cho mỗi multipart file/request, 5 MiB cho avatar và 15 MiB cho ảnh bài viết. Test signature JPEG/PNG/GIF/WebP hợp lệ và xác nhận SVG hoặc file giả dạng ảnh bị từ chối.

## Chuẩn bị giá trị triển khai AWS

Dùng placeholder trong report và giữ giá trị thật ngoài Git:

| Input | Giá trị an toàn trong report |
|---|---|
| EC2 public address | `<EC2_PUBLIC_IP>` |
| RDS endpoint | `<RDS_ENDPOINT>` |
| Database password | `<DB_PASSWORD>` |
| Public IP cá nhân | `<MY_PUBLIC_IP>` |
| SES sender | `<SES_VERIFIED_SENDER_EMAIL>` |
| SES admin recipient | `<SES_VERIFIED_ADMIN_EMAIL>` |
| Region | `ap-southeast-1` |
| S3 bucket | `techblog-uploads-ttv-2026` |

CloudWatch Agent sẽ được cài trên EC2 ở bước sau; máy local không cần agent.

## Chuẩn bị key pair và nguyên tắc bảo mật

Lưu PEM file ngoài repository, ví dụ `C:\WORKING\techblog-key.pem`. Không đưa nội dung PEM vào Markdown hoặc screenshot. Lên kế hoạch inbound rule:

- SSH `22` chỉ từ `<MY_PUBLIC_IP>`.
- TCP `8080` public chỉ trong thời gian demo.
- RDS `3306` chỉ từ EC2 Security Group.

Không cần static AWS credential vì `techblog-ec2-role` được gắn vào EC2.

## Checklist trước khi tạo tài nguyên

- [ ] Website local chạy tại `http://localhost:8080`.
- [ ] Kịch bản Guest, USER, EDITOR và ADMIN pass ở local.
- [ ] Java 17 và Maven Wrapper command hoạt động.
- [ ] Đã tạo `target/techblog-0.0.1-SNAPSHOT.jar`.
- [ ] Database local `techblog` sẵn sàng cho `mysqldump`.
- [ ] Cấu hình AWS SDK S3/SES tồn tại và không có static credential.
- [ ] Region là `ap-southeast-1`.
- [ ] AWS Budgets sẽ được tạo đầu tiên.
- [ ] PEM file nằm ngoài Git.
- [ ] Đã xác nhận SES Sandbox, verified sender và verified admin recipient.
- [ ] Nhóm hiểu quy tắc che thông tin nhạy cảm trong screenshot.

## Kết quả mong đợi

Nhóm có local baseline lặp lại được, JAR build thành công, placeholder an toàn và account prerequisite cần thiết để bắt đầu module 5.3.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| `java` không phải version 17 | Chọn Java 17 JDK trong `JAVA_HOME` và IntelliJ. |
| Maven Wrapper fail | Xác nhận có `.mvn/`, `mvnw`, `mvnw.cmd` và có network để tải dependency. |
| Local role test fail | Sửa application baseline trước khi provision AWS. |
| Phát hiện static AWS key trong config | Xóa key và dùng EC2 IAM Role/default provider chain. |
| SES identity nằm sai Region | Tạo/verify identity trong `ap-southeast-1`. |

## Ghi chú chi phí và bảo mật

Giai đoạn chuẩn bị không cần chargeable compute. Kiểm tra Region trước khi tạo tài nguyên, tránh dùng root và tạo Budget guardrail đầu tiên.

<!-- TODO: Thêm /images/5-Workshop/5.2-Prerequisites/local-build-success.png. Caption: *Hình 5.2.1. Maven Wrapper build local thành công.* -->
<!-- TODO: Thêm /images/5-Workshop/5.2-Prerequisites/region-selection.png. Caption: *Hình 5.2.2. Chọn Asia Pacific (Singapore) trong AWS Console.* -->
