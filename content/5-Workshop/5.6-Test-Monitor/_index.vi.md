---
title: "Kiểm thử, giám sát và xử lý sự cố"
date: 2026-07-10
weight: 6
pre: " <b> 5.6. </b> "
---

## Kiểm thử chức năng

Thực hiện checklist trên endpoint public:

- Reader đăng ký, đăng nhập, đọc, like, save, comment, reply, report và cập nhật avatar.
- Writer gửi yêu cầu nâng cấp, tạo/sửa/xóa bài và theo dõi trạng thái `PENDING`, `APPROVED`, `REJECTED`.
- Admin quản lý user, category, comment, report; duyệt writer request và bài viết.
- Dữ liệu xuất hiện trong RDS và ảnh xuất hiện trong đúng prefix S3.
- Session, CSRF, redirect và upload hoạt động qua CloudFront/WAF.

## CloudWatch và SNS

1. Cài và cấu hình CloudWatch Agent hoặc đẩy log ứng dụng vào CloudWatch Logs.
2. Đặt retention phù hợp để tránh lưu log demo vô thời hạn.
3. Tạo alarm cho CPU EC2, trạng thái instance, dung lượng/kết nối RDS và lỗi ứng dụng phù hợp.
4. Tạo SNS topic, đăng ký email và xác nhận subscription.
5. Kích hoạt một alarm thử nghiệm và kiểm tra email nhận được.

## Xử lý sự cố thường gặp

| Hiện tượng | Kiểm tra |
|---|---|
| EC2 không kết nối RDS | Endpoint, port 3306, Security Group source, VPC và secret |
| Ứng dụng không khởi động | Java 17, biến môi trường, quyền đọc secret và log Spring Boot |
| Upload S3 bị AccessDenied | IAM Role, bucket ARN, prefix, Region và Block Public Access |
| Đăng nhập lỗi qua CloudFront | Forward cookie/header, cache behavior, HTTPS redirect và CSRF |
| WAF chặn request hợp lệ | WAF sampled requests, rule gây chặn và chế độ Count để tinh chỉnh |

Cuối cùng, kiểm tra Billing và AWS Budgets để xác nhận chi phí nằm trong ngưỡng dự kiến.
