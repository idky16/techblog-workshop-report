---
title: "Build và upload Spring Boot JAR"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

## Mục tiêu

Build TechBlog executable JAR ở local và upload lên EC2.

## Điều kiện trước khi thực hiện

- Local build tools đã sẵn sàng.
- SSH đến EC2 hoạt động.

## Các bước thực hiện

Build trên Windows:

```powershell
.\mvnw.cmd clean package -DskipTests
```

Upload JAR:

```powershell
scp -i "C:\WORKING\techblog-key.pem" "C:\WORKING\techblog\target\techblog-0.0.1-SNAPSHOT.jar" ec2-user@<EC2_PUBLIC_IP>:/home/ec2-user/techblog.jar
```

Kiểm tra dung lượng file trên EC2:

```bash
ls -lh /home/ec2-user/techblog.jar
```

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| Build command | `.\mvnw.cmd clean package -DskipTests` |
| Remote path | `/home/ec2-user/techblog.jar` |
| JAR size | Khoảng 58 MB |

## Cách kiểm tra

JAR tồn tại trên EC2 và có dung lượng hợp lý.

## Kết quả mong đợi

Executable JAR sẵn sàng để chạy bằng `systemd`.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Upload fail | Kiểm tra PEM permission và EC2 public IP hiện tại. |
| Sai JAR path | Kiểm tra thư mục `target` sau khi build. |

## Ghi chú chi phí và bảo mật

Không đưa password vào command upload hoặc screenshot trong report.

<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/jar-upload.png. Caption: *Hình 5.5.4.1. TechBlog executable JAR đã upload lên EC2.* -->
