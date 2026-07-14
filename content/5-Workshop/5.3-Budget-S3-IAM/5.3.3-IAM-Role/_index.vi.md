---
title: "Tạo và chuẩn bị EC2 IAM Role"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

## Mục tiêu

Tạo `techblog-ec2-role` và chuẩn bị các policy của deployment cuối. EC2 chưa tồn tại nên trang này không gắn role vào instance hoặc chạy AWS CLI trên EC2.

## Điều kiện trước khi thực hiện

- S3 bucket `techblog-uploads-ttv-2026` đã tồn tại.
- Đã xác định permission cần cho S3 và CloudWatch.
- SES identity ARN sẽ có sau 5.3.4; tạm dùng `<SES_VERIFIED_IDENTITY_ARN>`.

## Các bước thực hiện

1. Mở **AWS IAM → Roles → Create role**.
2. Chọn **AWS service** làm trusted entity và **EC2** làm use case.
3. Đặt role name `techblog-ec2-role`.
4. Gắn managed policy `AmazonS3FullAccess`, là policy bản demo đã xác minh đang dùng.
5. Gắn managed policy `CloudWatchAgentServerPolicy` cho CloudWatch Agent.
6. Chuẩn bị inline policy `TechBlogSesSendEmail` chỉ gồm `ses:SendEmail` và `ses:SendRawEmail`. Giữ `<SES_VERIFIED_IDENTITY_ARN>` đến khi 5.3.4 tạo identity thật.
7. Lưu role nhưng chưa gắn vào instance.
8. Ghi nhận ứng dụng sẽ dùng AWS SDK default credential provider chain; không tạo Access Key cho IAM user.

## Cấu hình hiện tại

| Hạng mục | Giá trị đã xác minh |
|---|---|
| Role | `techblog-ec2-role` |
| Trusted service | Amazon EC2 |
| S3 managed policy | `AmazonS3FullAccess` |
| CloudWatch managed policy | `CloudWatchAgentServerPolicy` |
| SES inline policy | `TechBlogSesSendEmail` |
| SES action | `ses:SendEmail`, `ses:SendRawEmail` |
| Static credential | Không sử dụng |

`AmazonS3FullAccess` rộng hơn nhu cầu TechBlog. Nội dung ghi policy này vì đây là cấu hình thật của demo, không phải vì đây là policy cuối được khuyến nghị. Cải tiến bảo mật tương lai là thay bằng `ListBucket` trên TechBlog bucket và chỉ cho `GetObject`, `PutObject`, `DeleteObject` trong `avatars/*`, `posts/*`.

## Cách kiểm tra

1. Review **Trust relationships** và xác nhận EC2 service principal.
2. Review attached policy và inline policy `TechBlogSesSendEmail`.
3. Xác nhận role tồn tại và không tạo access key cho role.
4. Sau 5.3.4, thay `<SES_VERIFIED_IDENTITY_ARN>` và review inline policy cuối trên IAM console.
5. Để việc gắn role và test runtime S3/SES/CloudWatch cho module 5.5 và 5.6.

## Kết quả mong đợi

`techblog-ec2-role` sẵn sàng để gắn khi launch EC2 ở 5.5.1. Policy cuối khớp trạng thái deployment, không có static key và trang này chưa tuyên bố runtime test thành công.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Chưa có EC2 để kiểm tra | Đây là trạng thái đúng; EC2 được tạo ở 5.5, không tạo instance tạm chỉ để test role. |
| Chưa hoàn thiện được SES policy | Hoàn tất 5.3.4 rồi thay `<SES_VERIFIED_IDENTITY_ARN>` bằng ARN của identity đã verify. |
| Sai trusted entity | Sửa trust policy để EC2 service có thể assume role. |
| Gọi S3 policy là least privilege | Sửa report: demo hiện dùng `AmazonS3FullAccess` rộng; thu hẹp quyền là hướng phát triển. |

## Ghi chú chi phí và bảo mật

IAM không có direct service charge. Không xử lý authorization error bằng long-lived key. Chỉ thu hẹp `AmazonS3FullAccess` sau khi xác nhận và kiểm thử đầy đủ object operation cần thiết.

<!-- TODO: Thêm /images/5-Workshop/5.3-Budget-S3-IAM/iam-role-policies.png. Caption: *Hình 5.3.3.1. Trust relationship và policy đã chuẩn bị của techblog-ec2-role trước khi tạo EC2, sau khi che thông tin.* -->
