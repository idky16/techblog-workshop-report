---
title: "Tạo và bảo vệ S3 bucket"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## Mục tiêu

Tạo S3 bucket private để TechBlog lưu object avatar và ảnh bài viết. Bucket là application storage, không phải static website hosting.

## Điều kiện trước khi thực hiện

- Region: `ap-southeast-1`.
- Bucket name: `techblog-uploads-ttv-2026`.
- Ứng dụng có AWS SDK for Java S3 integration và sẽ chạy bằng EC2 IAM Role.

## Các bước thực hiện

1. Mở **Amazon S3** và chọn **Create bucket**.
2. Nhập `techblog-uploads-ttv-2026`.
3. Chọn **Asia Pacific (Singapore) – ap-southeast-1**.
4. Giữ **Object Ownership: ACLs disabled**.
5. Giữ toàn bộ **Block Public Access** option đang bật.
6. Bật default server-side encryption bằng **Amazon S3 managed keys (SSE-S3)**.
7. Tắt Bucket Versioning cho bản demo tiết kiệm chi phí.
8. Không bật Transfer Acceleration hoặc static website hosting.
9. Dùng **Create folder** để tạo hai prefix:
   - `avatars/`
   - `posts/`
10. Kiểm tra tab **Permissions** và **Properties** trước khi tiếp tục.

## Cấu hình

| Thiết lập | Giá trị |
|---|---|
| Bucket | `techblog-uploads-ttv-2026` |
| Region | `ap-southeast-1` |
| Object Ownership | ACLs disabled |
| Public access | Bị chặn |
| Default encryption | SSE-S3 |
| Versioning | Disabled cho demo |
| Prefix | `avatars/`, `posts/` |
| Website hosting | Disabled |

## Quy ước application storage

Spring Boot dùng AWS SDK operation để upload, đọc, thay thế và xóa object thông qua `techblog-ec2-role`. Ứng dụng đã triển khai lưu database marker theo định dạng:

```text
s3:avatars/<uuid>.<ext>
s3:posts/<uuid>.<ext>
```

Prefix `s3:` là marker của ứng dụng, không phải một phần của S3 object key. Object key thật là `avatars/<uuid>.<ext>` và `posts/<uuid>.<ext>`. MySQL lưu marker, không lưu binary ảnh hoặc presigned URL. Vì bucket private, ứng dụng tạo presigned GET URL có thời hạn 10 phút khi render. Reference ảnh local legacy tiếp tục dùng local path hiện có.

## Cách kiểm tra

Trong S3 console, xác nhận:

- Block Public Access hiển thị **On**.
- Default encryption hiển thị **SSE-S3**.
- Có `avatars/` và `posts/`.
- Không có bucket policy hoặc object ACL cho public access.

Kiểm thử object ở application level được thực hiện trong 5.6.2 sau khi cấu hình EC2 role và runtime value.

## Kết quả mong đợi

Bucket private đã tích hợp với TechBlog. Các thao tác tạo, thay và xóa avatar/ảnh bài viết mới dùng đúng prefix mà không public bucket.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Bucket name đã tồn tại | Xác nhận đúng tên globally unique của nhóm; không âm thầm ghi bucket khác trong report. |
| EC2 gặp AccessDenied | Kiểm tra policy resource của role và prefix chính xác. |
| Image URL trả 403 | Kiểm tra ứng dụng dùng đúng controlled-access method; không chuyển bucket thành public. |
| Object nằm sai prefix | Kiểm tra cách tạo key trong avatar/post storage service. |

## Ghi chú chi phí và bảo mật

Giữ test file nhỏ, xóa lifecycle test object sau khi dùng và tắt Transfer Acceleration. Block Public Access phải luôn bật kể cả khi debug hiển thị ảnh.

<!-- TODO: Thêm /images/5-Workshop/5.3-Budget-S3-IAM/s3-bucket-settings.png. Caption: *Hình 5.3.2.1. TechBlog bucket private với Block Public Access và SSE-S3.* -->
<!-- TODO: Thêm /images/5-Workshop/5.3-Budget-S3-IAM/s3-prefixes.png. Caption: *Hình 5.3.2.2. Hai prefix avatars/ và posts/.* -->
