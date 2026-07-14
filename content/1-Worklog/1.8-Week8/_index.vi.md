---
title: "Tuần 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

## Thời gian

08/06/2026 - 14/06/2026

## Mục tiêu trong tuần

- Chuyển thao tác avatar và ảnh bài viết mới sang Amazon S3 private.
- Giữ tương thích ảnh local legacy và tăng độ an toàn của upload.

## Công việc đã thực hiện

- Tích hợp AWS SDK for Java v2 thông qua storage abstraction có local provider và S3 provider.
- Lưu reference mới dạng `s3:avatars/<uuid>.<ext>` hoặc `s3:posts/<uuid>.<ext>`; chỉ tạo presigned GET URL 10 phút khi render.
- Giữ tương thích reference legacy `/images/avatars/...`, `posts/...` với các thư mục local hiện có.
- Thêm signature validation cho JPEG, PNG, GIF, WebP; từ chối file rỗng, SVG, giả dạng ảnh hoặc vượt giới hạn.
- Sửa regression HTTP 413 bằng multipart transport limit 50 MB, đồng thời giữ business limit avatar 5 MiB và ảnh bài viết 15 MiB.

## Kết quả và bài học

- Kiểm tra thành công upload, hiển thị, thay thế và xóa object avatar/ảnh bài viết.
- Xác nhận bucket vẫn private, không dùng public ACL, public bucket policy hoặc browser CORS.
- Phân biệt được giới hạn parse multipart của HTTP với application validation chặt hơn.
