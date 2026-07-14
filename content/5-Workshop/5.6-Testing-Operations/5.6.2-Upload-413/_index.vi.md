---
title: "Kiểm thử S3 upload và xử lý HTTP 413"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## Mục tiêu

Kiểm tra toàn bộ S3 image lifecycle cho avatar và ảnh bài viết, ghi lại lỗi HTTP 413 thật và phân biệt giới hạn transport của Spring với giới hạn ảnh ở application layer.

## Điều kiện trước khi thực hiện

- TechBlog chạy với `techblog-ec2-role`.
- Đã cấu hình `AWS_REGION`, `TECHBLOG_STORAGE_PROVIDER=s3`, `TECHBLOG_S3_BUCKET` và `TECHBLOG_PRESIGN_TTL_MINUTES=10`.
- S3 bucket private có `avatars/` và `posts/`.
- Có avatar PNG test không nhạy cảm khoảng 2,09 MB và một post-image test file.

## Các bước thực hiện

### 1. Kiểm thử tạo avatar

1. Sign in bằng USER/Reader.
2. Upload avatar test.
3. Mở S3 và xác nhận một object mới xuất hiện trong `avatars/`.
4. Refresh profile và xác nhận ảnh private hiển thị qua controlled-access method của ứng dụng.
5. Kiểm tra RDS field tương ứng và xác nhận field lưu `s3:avatars/<uuid>.<ext>`, không lưu binary hoặc presigned URL.

### 2. Kiểm thử thay và xóa avatar

1. Thay avatar bằng ảnh test thứ hai.
2. Xác nhận profile hiển thị ảnh mới.
3. Xác nhận database reference đổi đúng.
4. Xác nhận object cũ bị xóa hoặc chỉ được giữ nếu source code chủ động version object.
5. Nếu UI hỗ trợ remove avatar, thực hiện và kiểm tra hành vi S3 tương ứng.

### 3. Kiểm thử post-image lifecycle

1. Sign in bằng EDITOR/Writer và tạo bài có ảnh.
2. Xác nhận object xuất hiện trong `posts/`.
3. Xác nhận post record lưu `s3:posts/<uuid>.<ext>`.
4. Sửa hoặc xóa ảnh bài viết và kiểm tra ứng dụng xử lý object cũ theo implementation.
5. Hoàn tất Admin approval để bảo đảm public post vẫn hiển thị ảnh private đúng.

## Lỗi HTTP 413 và giới hạn hiện tại

Một artifact cũ từng trả HTTP 413 với avatar PNG khoảng 2,09 MB. Source hiện tại cấu hình multipart transport layer của Spring như sau:

```properties
spring.servlet.multipart.enabled=true
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=50MB
```

Hai giá trị 50 MB chỉ quyết định Spring có nhận multipart request hay không. Sau khi request được parse, `ImageFileValidator` áp dụng business rule chặt hơn: avatar tối đa 5 MiB, ảnh bài viết tối đa 15 MiB. Validator đọc signature và chỉ chấp nhận JPEG, PNG, GIF hoặc WebP; SVG và file chỉ giả extension ảnh bị từ chối. Vì vậy ảnh 2,09 MB là test regression đã xác minh, không phải giới hạn cấu hình.

Rebuild và upload JAR mới, sau đó backup và thay artifact đang chạy:

```bash
cp /home/ec2-user/techblog.jar /home/ec2-user/techblog-backup.jar
mv /home/ec2-user/techblog-new.jar /home/ec2-user/techblog.jar
sudo systemctl restart techblog
curl -I http://localhost:8080
```

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| Avatar prefix | `avatars/` |
| Post-image prefix | `posts/` |
| Lỗi test | HTTP 413 với PNG khoảng 2,09 MB |
| Multipart file limit | 50 MB |
| Multipart request limit | 50 MB |
| Giới hạn avatar ở application | 5 MiB |
| Giới hạn ảnh bài viết ở application | 15 MiB |
| Signature được chấp nhận | JPEG, PNG, GIF, WebP |
| Trường hợp bị từ chối | File rỗng, SVG, file giả dạng ảnh, file vượt business limit tương ứng |
| Bucket access | Private |
| Nội dung database | Chỉ marker `s3:avatars/...` hoặc `s3:posts/...` |
| Cách hiển thị | Presigned GET URL có thời hạn 10 phút |

## Cách kiểm tra

Lưu một chuỗi before/after đã che thông tin cho mỗi loại upload:

- UI action thành công.
- Object nằm đúng S3 prefix.
- Database reference khớp key dự kiến.
- Ảnh private hiển thị qua presigned GET URL 10 phút; ảnh local legacy vẫn render.
- Flow replace/delete đã xác minh xóa managed S3 object tương ứng và không xóa reference local legacy.
- `curl` trả HTTP 200 sau redeploy.

## Kết quả mong đợi

Avatar và post-image operation dùng S3 đúng. Avatar 2,09 MB không còn trả HTTP 413, còn ảnh quá dung lượng hoặc không đúng định dạng vẫn bị application rule tương ứng từ chối.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| HTTP 413 vẫn còn | Xác nhận JAR đang chạy chứa setting 50 MB và không có upstream layer với limit nhỏ hơn. |
| Request được nhận nhưng validation fail | So sánh file với giới hạn avatar 5 MiB hoặc ảnh bài viết 15 MiB và kiểm tra signature JPEG/PNG/GIF/WebP. |
| S3 AccessDenied | Kiểm tra instance role, bucket ARN, prefix và API action. |
| Object tồn tại nhưng ảnh bị lỗi | Kiểm tra controlled-access URL/response và database object reference. |
| Object cũ còn sau khi thay | Review delete behavior và chỉ thêm cleanup action nếu đúng business rule. |
| Local upload pass nhưng EC2 fail | So sánh runtime Region, bucket variable, role và active JAR version. |

## Ghi chú chi phí và bảo mật

Từ chối file type không hỗ trợ, validate size/signature, dùng generated object name và xóa test object. Không cho phép SVG hoặc chuyển bucket thành public để sửa lỗi hiển thị ảnh.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/http-413.png. Caption: *Hình 5.6.2.1. HTTP 413 trước khi cập nhật multipart limit.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/s3-avatars-objects.png. Caption: *Hình 5.6.2.2. Object avatar đã che thông tin trong avatars/.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/s3-posts-objects.png. Caption: *Hình 5.6.2.3. Object ảnh bài viết đã che thông tin trong posts/.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/avatar-upload-success.png. Caption: *Hình 5.6.2.4. Upload avatar thành công sau bản sửa multipart.* -->
