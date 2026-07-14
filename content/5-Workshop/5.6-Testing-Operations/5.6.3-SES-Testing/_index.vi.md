---
title: "Kiểm thử Amazon SES và thông báo đăng ký"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

## Mục tiêu

Kiểm tra Amazon SES tại ba ranh giới: SES console, AWS CLI trên EC2 qua `techblog-ec2-role` và luồng registration của Spring Boot. Ứng dụng gửi email cuối đến quản trị viên được cấu hình thay vì địa chỉ đăng ký.

## Điều kiện trước khi thực hiện

- SES email identity ở trạng thái **Verified** trong `ap-southeast-1`.
- Account vẫn ở Sandbox; sender và admin recipient đều đã verify.
- `techblog-ec2-role` có inline policy `TechBlogSesSendEmail`.
- Đã cấu hình `TECHBLOG_SES_ENABLED=true`, `TECHBLOG_SES_FROM_EMAIL=<SES_VERIFIED_SENDER_EMAIL>` và `TECHBLOG_SES_ADMIN_RECIPIENT=<SES_VERIFIED_ADMIN_EMAIL>`.
- Không cấu hình SMTP credential, Access Key hoặc Secret Access Key.

## Các bước thực hiện

### 1. Kiểm thử từ Amazon SES console

1. Mở **Amazon SES → Configuration → Identities** tại `ap-southeast-1`.
2. Chọn verified email identity và chọn **Send test email**.
3. Dùng admin recipient đã verify vì account đang ở Sandbox.
4. Gửi plain-text message với nội dung test không nhạy cảm.
5. Xác nhận recipient nhận được email mà không ghi email thật hoặc message identifier vào report.

### 2. Kiểm thử từ EC2 qua IAM Role

Trên `techblog-ec2`, xác nhận caller dùng instance role, sau đó gửi một SES v2 message nhỏ bằng placeholder:

```bash
aws sts get-caller-identity

aws sesv2 send-email \
  --from-email-address "<SES_VERIFIED_SENDER_EMAIL>" \
  --destination "ToAddresses=<SES_VERIFIED_ADMIN_EMAIL>" \
  --content 'Simple={Subject={Data=TechBlog SES IAM Role Test},Body={Text={Data=SES call from techblog-ec2 via IAM Role}}}' \
  --region ap-southeast-1
```

Không đưa message ID trả về, account ID hoặc email thật vào report. Test đã xác minh này chứng minh EC2 gọi SES được mà không cần static key.

### 3. Kiểm thử end-to-end registration notification

1. Mở trang sign-up của TechBlog và đăng ký một test user không chứa dữ liệu nhạy cảm.
2. Xác nhận registration thành công và user có thể sign in.
3. Xác nhận admin recipient được cấu hình nhận subject **TechBlog - New User Registration**.
4. Kiểm tra plain-text body chỉ có username, display name nếu có, registration email và registration time.
5. Xác nhận địa chỉ đăng ký không được dùng làm SES recipient.
6. Review application log cho success/failure event ngắn, không chứa full email content hoặc identifier.
7. Nếu có controlled SES failure, xác nhận user đã lưu vẫn tồn tại và sign in được.

## Cấu hình

| Hạng mục | Giá trị đã xác minh |
|---|---|
| Region | `ap-southeast-1` |
| Trạng thái account | Sandbox |
| Sender | `<SES_VERIFIED_SENDER_EMAIL>` |
| Recipient | `<SES_VERIFIED_ADMIN_EMAIL>` |
| Application subject | `TechBlog - New User Registration` |
| Nguồn credential | Default credential chain của `techblog-ec2-role` |
| Hành vi khi lỗi | Registration vẫn thành công; ghi WARN đã sanitize |

## Cách kiểm tra

- SES console test gửi đến admin recipient đã verify thành công.
- AWS CLI send trên EC2 thành công qua IAM Role.
- Registration được lưu thành công gọi đúng một admin notification.
- Message không chứa password, password hash, JWT, cookie, credential hoặc database secret.
- SES lỗi không làm xuất hiện error page hoặc rollback user record.

## Kết quả mong đợi

Admin đã verify nhận operational registration notification khi SES thành công, trong khi registration vẫn độc lập với lỗi SES tạm thời.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| `MessageRejected` | Kiểm tra Sandbox và verify sender/admin recipient tại `ap-southeast-1`. |
| `AccessDeniedException` | Kiểm tra `TechBlogSesSendEmail`, verified identity resource và role gắn vào EC2. |
| CLI dùng identity khác | Chạy `aws sts get-caller-identity` và loại static/profile credential ngoài ý muốn. |
| Console/CLI pass nhưng application không gửi | Kiểm tra ba biến `TECHBLOG_SES_*` rồi restart `techblog.service`. |
| Registration fail khi SES fail | Giữ notification theo best-effort và catch send exception tại notification boundary. |

## Ghi chú chi phí và bảo mật

Chỉ gửi ít plain-text test message. Không dùng attachment, bulk sending, SMTP credential, Dedicated IP, Mail Manager, Global Endpoint, domain identity hoặc tuyên bố production access.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/ses-console-test.png. Caption: *Hình 5.6.3.1. SES console test thành công đến verified Sandbox recipient sau khi che thông tin.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/ses-cli-role-test.png. Caption: *Hình 5.6.3.2. SES CLI test từ EC2 qua techblog-ec2-role sau khi che thông tin.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/ses-admin-registration-notice.png. Caption: *Hình 5.6.3.3. Admin notification sau khi đăng ký TechBlog thành công và đã che thông tin.* -->
