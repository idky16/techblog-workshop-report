---
title: "SSH và cài Java 17"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Mục tiêu

Kết nối EC2 từ Windows, cài Java 17 Amazon Corretto và MariaDB client cần cho bước import RDS.

## Điều kiện trước khi thực hiện

- PEM key nằm tại `C:\WORKING\techblog-key.pem`.
- Public IP của EC2 được ghi bằng `<EC2_PUBLIC_IP>`.

## Các bước thực hiện

Sửa permission file PEM trên Windows:

```powershell
icacls "C:\WORKING\techblog-key.pem" /inheritance:r
icacls "C:\WORKING\techblog-key.pem" /remove "BUILTIN\Users"
icacls "C:\WORKING\techblog-key.pem" /grant:r "$($env:USERNAME):(R)"
```

Kết nối bằng SSH:

```powershell
ssh -i "C:\WORKING\techblog-key.pem" ec2-user@<EC2_PUBLIC_IP>
```

Cài Java 17:

```bash
sudo dnf update -y
sudo dnf install -y java-17-amazon-corretto-headless
sudo dnf install -y mariadb105
java -version
```

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| User | `ec2-user` |
| Key | `C:\WORKING\techblog-key.pem` |
| Java | Amazon Corretto 17 |
| Database client | MariaDB client tương thích với RDS MySQL |

## Cách kiểm tra

`java -version` cần hiển thị Java 17 và `mysql --version` hiển thị client đã cài.

## Kết quả mong đợi

EC2 có thể chạy Spring Boot JAR.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| PEM permission quá rộng | Chạy các lệnh `icacls`. |
| SSH timeout | Cập nhật SSH source IP thành `<MY_PUBLIC_IP>` hiện tại. |

## Ghi chú chi phí và bảo mật

Không upload PEM file lên Git hoặc đưa vào screenshot.

<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/java-17.png. Caption: *Hình 5.5.2.1. Java 17 Amazon Corretto đã cài trên EC2.* -->
