---
title: "Cấu hình Amazon SES verified email identity"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

## Mục tiêu

Cấu hình verified email identity dùng để gửi best-effort operational notification đến quản trị viên TechBlog sau khi registration của user mới được lưu thành công.

## Điều kiện trước khi thực hiện

- Region là `ap-southeast-1`.
- Nhóm kiểm soát sender mailbox được biểu diễn bằng `<SES_VERIFIED_SENDER_EMAIL>`.
- Admin recipient được cấu hình, biểu diễn bằng `<SES_VERIFIED_ADMIN_EMAIL>`, cũng có thể verify vì account vẫn ở Sandbox.

## Các bước thực hiện

1. Mở **Amazon SES** và chọn `ap-southeast-1`.
2. Mở **Configuration → Identities → Create identity**.
3. Chọn **Email address**. Demo cuối không dùng domain identity.
4. Nhập sender address; trong report dùng `<SES_VERIFIED_SENDER_EMAIL>`.
5. Mở verification message và hoàn tất verification link.
6. Quay lại SES, xác nhận status **Verified**.
7. Xác nhận admin recipient cũng là verified address khi account còn ở Sandbox.
8. Mở **Account dashboard** và ghi nhận Sandbox state mà không lộ email hoặc account detail.

## Cấu hình

| Thiết lập | Giá trị đã xác minh |
|---|---|
| Region | `ap-southeast-1` |
| Identity type | Email address |
| Sender placeholder | `<SES_VERIFIED_SENDER_EMAIL>` |
| Admin placeholder | `<SES_VERIFIED_ADMIN_EMAIL>` |
| Trạng thái account | Sandbox |
| Mục đích ứng dụng | Thông báo user mới cho quản trị viên |
| SMTP credential | Không sử dụng |
| Domain identity | Không sử dụng |

Email chỉ gồm username, display name nếu có, registration email và registration time. Email tuyệt đối không chứa password, password hash, JWT, cookie, AWS credential hoặc database secret.

## Quy tắc Sandbox

SES Sandbox chỉ cho gửi từ verified identity và đến verified recipient. Vì vậy, TechBlog gửi operational notification đến admin recipient đã verify và được cấu hình, không gửi đến mọi email mà user nhập khi đăng ký. Nhóm không request production access.

## Cách kiểm tra

- Email identity ở trạng thái **Verified** trong `ap-southeast-1`.
- Chỉ dùng identity ARN thật trong policy workflow đã che thông tin; trong report giữ `<SES_VERIFIED_IDENTITY_ARN>`.
- Hoàn thiện hoặc review `TechBlogSesSendEmail` để chỉ có `ses:SendEmail`, `ses:SendRawEmail` cho identity này.
- Chưa chạy EC2 CLI test ở đây. Việc gắn role và runtime test diễn ra sau khi tạo EC2 trong module 5.5 và 5.6.

## Kết quả mong đợi

Sender và admin recipient đáp ứng Sandbox boundary. Policy giới hạn theo identity đã sẵn sàng; IAM Role CLI test và end-to-end application test được thực hiện ở 5.6.3 sau khi EC2 tồn tại.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Thiếu verification message | Kiểm tra spam, xác nhận address và resend verification. |
| Identity verify ở Region khác | Chuyển sang `ap-southeast-1` và verify tại đây. |
| `MessageRejected` trong Sandbox | Xác nhận sender và admin recipient đều đã verify. |

## Ghi chú chi phí và bảo mật

Chỉ gửi ít plain-text test message. Không dùng SMTP credential, attachment, Dedicated IP, Mail Manager, Global Endpoint hoặc domain identity.

<!-- TODO: Thêm /images/5-Workshop/5.3-Budget-S3-IAM/ses-verified-identity.png. Caption: *Hình 5.3.4.1. Amazon SES email identity và Sandbox state đã che thông tin tại ap-southeast-1.* -->
