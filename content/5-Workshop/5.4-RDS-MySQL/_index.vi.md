---
title: "Chuẩn bị RDS MySQL"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
alwaysopen: true
---

## Mục tiêu module

Module này tạo Amazon RDS for MySQL private và chuẩn bị mọi thành phần cần thiết trước khi EC2 tồn tại: ranh giới Security Group, placeholder kết nối an toàn và SQL dump local. Lệnh kết nối/import được chuyển sang 5.5.3.

## Luồng module

{{< mermaid align="center" >}}
flowchart LR
    L[Local MySQL<br/>techblog] -->|mysqldump| F[techblog.sql]
    E[EC2 Security Group<br/>chỉ mới chuẩn bị] -->|source reference :3306| D[RDS Security Group]
    D --> R[(Amazon RDS for MySQL<br/>techblog-db)]
    F -. upload và import ở 5.5.3 .-> R
{{< /mermaid >}}

## Trang con

1. [Tạo RDS MySQL](5.4.1-create-rds/)
2. [Cấu hình Security Group EC2-RDS](5.4.2-security-groups/)
3. [Ghi nhận cấu hình kết nối RDS](5.4.3-connection-settings/)
4. [Export database TechBlog ở local](5.4.4-export-database/)

## Kết quả module

| Hạng mục | Kết quả mong đợi |
|---|---|
| RDS | `techblog-db` đạt trạng thái available. |
| Security | Inbound rule của RDS tham chiếu EC2 Security Group đã chuẩn bị trên port `3306`. |
| Giá trị kết nối | Ghi `<RDS_ENDPOINT>`, database `techblog`, port `3306` và username mà không lộ secret. |
| Local export | `techblog.sql` tồn tại ở local và sẵn sàng chuyển sau khi tạo EC2. |

Module này chưa chạy SSH, `mysql`, `scp` hoặc import RDS. MySQL không lưu binary ảnh hay presigned URL; sau deployment, media reference mới dùng `s3:avatars/<uuid>.<ext>` hoặc `s3:posts/<uuid>.<ext>` và reference ảnh local legacy được giữ nguyên.
