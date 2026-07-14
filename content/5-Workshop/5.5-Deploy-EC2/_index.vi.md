---
title: "Triển khai Spring Boot lên EC2"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
alwaysopen: true
---

## Mục tiêu module

Tạo Amazon EC2, gắn IAM Role/Security Group đã chuẩn bị, import database local vào RDS từ EC2, deploy TechBlog JAR, cấu hình runtime integration và duy trì ứng dụng bằng `systemd`.

## Luồng module

{{< mermaid align="center" >}}
flowchart LR
    D[Local techblog.sql] -->|scp và mysql import| E[Amazon EC2<br/>techblog-ec2]
    E --> R[(Amazon RDS for MySQL)]
    L[Local Windows<br/>Maven Wrapper] -->|build và scp| J[techblog.jar]
    J --> E
    C[Environment file giới hạn quyền<br/>RDS + S3 + SES + logging] --> E
    E -->|systemd| S[techblog.service]
    S -->|HTTP :8080| W[TechBlog website]
{{< /mermaid >}}

## Trang con

1. [Tạo EC2 và cấu hình Security Group](5.5.1-create-ec2/)
2. [SSH và cài Java 17](5.5.2-ssh-java/)
3. [Kết nối RDS và import database](5.5.3-rds-import/)
4. [Build và upload Spring Boot JAR](5.5.4-build-upload-jar/)
5. [Cấu hình runtime integration cho RDS, S3 và SES](5.5.5-runtime-integration/)
6. [Chạy TechBlog bằng systemd](5.5.6-systemd/)

## Kết quả module

| Hạng mục | Kết quả mong đợi |
|---|---|
| EC2 | `techblog-ec2` chạy Amazon Linux 2023 với `techblog-ec2-role`. |
| Java | Đã cài Java 17 Amazon Corretto. |
| Database migration | EC2 kết nối RDS private và import chín bảng TechBlog. |
| JAR | Upload `techblog-0.0.1-SNAPSHOT.jar` thành `/home/ec2-user/techblog.jar`. |
| Runtime | Cấp RDS, S3, SES, Region và log-file value mà không dùng static AWS key. |
| Service | `techblog.service` ở trạng thái `active (running)`. |
| Website | Local và public port-`8080` check trả HTTP 200. |

## Ghi chú chi phí và bảo mật

Demo dùng một `t3.micro` và 8 GiB gp3. SSH chỉ mở cho `<MY_PUBLIC_IP>`; port `8080` public chỉ để demo. Environment file ở mode `600` và không xuất hiện trong screenshot.
