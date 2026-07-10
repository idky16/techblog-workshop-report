---
title: "Tích hợp S3, IAM, CloudFront và WAF"
date: 2026-07-10
weight: 5
pre: " <b> 5.5. </b> "
---

## Tạo S3 bucket

1. Tạo bucket duy nhất cho TechBlog và bật Block Public Access.
2. Bật mã hóa mặc định và cân nhắc versioning/lifecycle phù hợp.
3. Dùng prefix `avatars/` và `posts/` thay cho `storage/avatars` và `storage/posts` trên EC2.
4. Cấu hình ứng dụng kiểm tra MIME type, phần mở rộng, kích thước và sinh object key ngẫu nhiên.

IAM policy của EC2 chỉ nên cho phép các thao tác cần thiết trên bucket/prefix của TechBlog. Không dùng quyền `s3:*` trên mọi resource.

## Kiểm tra tích hợp upload

Đăng nhập, cập nhật avatar và tạo bài có ảnh. Xác nhận object xuất hiện đúng prefix, metadata phù hợp và ứng dụng đọc lại được ảnh sau khi restart EC2. Dữ liệu upload không được phụ thuộc ổ đĩa instance.

## Thêm CloudFront và AWS WAF

Sau khi endpoint EC2 hoạt động ổn định:

1. Tạo CloudFront distribution trỏ tới origin EC2 và chuyển tiếp method, header, cookie, query string mà Spring MVC cần.
2. Không cache response đăng nhập, quản trị hoặc nội dung cá nhân hóa; chỉ cache nội dung an toàn nếu đã kiểm thử.
3. Gắn AWS WAF Web ACL với managed rules cơ bản và theo dõi false positive.
4. Tạo rate-based rule cho login, register và comment; bắt đầu ở chế độ Count để chọn ngưỡng phù hợp, sau đó chuyển sang Block khi đã xác nhận không chặn nhầm người dùng hợp lệ.
5. Kiểm tra toàn bộ redirect, session cookie, CSRF và upload qua CloudFront.

Bản demo không yêu cầu Route 53 hoặc custom domain. Endpoint CloudFront là endpoint public cuối của workshop.
