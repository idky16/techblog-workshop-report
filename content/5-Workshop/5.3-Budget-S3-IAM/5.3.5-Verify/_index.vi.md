---
title: "Kiểm tra Budget, S3, IAM và SES"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.3.5. </b> "
---

## Mục tiêu

Review toàn bộ foundation trước khi tạo database và application runtime. Bước này tránh chẩn đoán nhầm lỗi cấu hình AWS thành lỗi Spring Boot.

## Điều kiện trước khi thực hiện

- AWS Budget đã tồn tại.
- S3 bucket `techblog-uploads-ttv-2026` đã tồn tại.
- IAM Role `techblog-ec2-role` đã tồn tại.
- SES sender identity đã verify trong `ap-southeast-1`.

## Các bước thực hiện

1. Mở AWS Budgets và xác nhận cost budget cùng notification threshold.
2. Mở S3 bucket, review Block Public Access, Object Ownership, SSE-S3, Region và prefix.
3. Mở `techblog-ec2-role` và review trust relationship.
4. Ghi nhận S3 access hiện tại là `AmazonS3FullAccess`; không gọi policy này là least privilege.
5. Xác nhận inline policy `TechBlogSesSendEmail` chỉ cho `ses:SendEmail`, `ses:SendRawEmail` đối với verified SES identity.
6. Xác nhận đã gắn `CloudWatchAgentServerPolicy` cho CloudWatch Agent/Logs.
7. Mở Amazon SES và xác nhận sender status **Verified**.
8. Ghi lại SES Sandbox state và xác nhận admin test recipient đã verify.
9. Xác nhận trên IAM rằng role không có long-lived access key; role chỉ nhận temporary credential sau khi được gắn ở 5.5.1.
10. Chỉ chụp console screenshot sau khi che account ID và email. Không chạy EC2, AWS CLI, SDK, S3 object, SES send hoặc CloudWatch Agent test trong trang này.

## Checklist cấu hình

| Hạng mục | Giá trị mong đợi |
|---|---|
| Budget | Active notification rule |
| S3 Region | `ap-southeast-1` |
| S3 access | Private, Block Public Access bật |
| S3 prefixes | `avatars/`, `posts/` |
| IAM Role | `techblog-ec2-role`, trusted by EC2 |
| S3 policy hiện tại | `AmazonS3FullAccess` (cần thu hẹp trong tương lai) |
| SES identity | Verified |
| SES account state | Sandbox; chỉ verified recipient |
| CloudWatch destination | `/techblog/application` |
| Static Access Key | Không có |

## Cách kiểm tra

Dùng quy tắc sau:

- Console configuration được xác minh bằng console view đã che thông tin nhạy cảm.
- IAM permission chỉ được xác minh sau khi EC2 application hoặc agent dùng thành công.
- S3 integration được xác minh sau bằng create/replace/delete, database marker và presigned URL 10 phút.
- SES integration được xác minh sau bằng console send, IAM Role CLI send và admin notification sau registration.

Quy tắc này tránh trình bày policy chưa test như application behavior thành công.

## Kết quả mong đợi

Toàn bộ foundation setting thống nhất và sẵn sàng cho RDS/EC2 deployment. Kết quả trang này chỉ xác nhận cấu hình trên console; runtime evidence được thu thập sau khi EC2 tồn tại.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Bucket hoặc SES sai Region | Tạo/verify trong `ap-southeast-1` và cập nhật runtime value. |
| S3 policy rộng hơn nhu cầu | Ghi đúng trạng thái hiện tại và lên kế hoạch thay bằng policy đã test, giới hạn bucket/prefix. |
| SES identity pending | Hoàn tất verification trước runtime test. |
| Đã tạo access key cho application | Xóa key và dùng EC2 instance profile sau 5.5.1. |

## Ghi chú chi phí và bảo mật

Bước review không có direct cost đáng kể. Không public Budget recipient email, SES sender, AWS Account ID hoặc policy detail có identity ARN thật.

<!-- TODO: Thêm /images/5-Workshop/5.3-Budget-S3-IAM/foundation-verification.png. Caption: *Hình 5.3.5.1. Kiểm tra Budget, S3, IAM và SES sau khi che thông tin nhạy cảm.* -->
