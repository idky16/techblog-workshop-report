---
title: "Tuần 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

## Thời gian

15/06/2026 - 21/06/2026

## Mục tiêu trong tuần

- Tích hợp transactional email nhỏ mà không làm thay đổi kết quả đăng ký.
- Tuân thủ giới hạn Amazon SES Sandbox và IAM Role.

## Công việc đã thực hiện

- Thêm AWS SDK v2 SES với `SesV2Client`, default credential chain và `techblog-ec2-role`.
- Cấu hình verified sender cùng verified admin recipient bằng environment variable.
- Chỉ gửi admin notification sau khi user mới được lưu thành công; email gồm username, display name, registration email và thời gian.
- Xử lý best-effort để lỗi Amazon SES chỉ tạo WARN ngắn, không rollback registration.
- Kiểm thử gửi bằng console, AWS CLI trên EC2 qua IAM Role và luồng registration của Spring Boot.

## Kết quả và bài học

- Xác minh admin registration notification hoạt động trong SES Sandbox.
- Xác nhận email/log không chứa password, password hash, JWT, cookie hoặc AWS credential.
- Ghi rõ Sandbox chỉ gửi tới verified recipient và đây không phải welcome-email workflow cho mọi user.
