---
title: "Dọn dẹp tài nguyên"
date: 2026-07-10
weight: 7
pre: " <b> 5.7. </b> "
---

## Trước khi xóa

- Lưu ảnh chụp màn hình, log và kết quả kiểm thử cần cho báo cáo.
- Export hoặc snapshot database nếu cần giữ dữ liệu.
- Tải object S3 cần lưu và xác nhận không còn dữ liệu quan trọng.
- Ghi lại cấu hình để có thể dựng lại môi trường.

## Thứ tự dọn dẹp

1. Disable rồi xóa CloudFront distribution sau khi deployment hoàn tất.
2. Gỡ liên kết và xóa AWS WAF Web ACL/rule không còn dùng.
3. Stop ứng dụng, terminate EC2 và xóa volume/Elastic IP không cần thiết.
4. Xóa RDS instance; chỉ giữ final snapshot khi thực sự cần vì snapshot vẫn phát sinh chi phí.
5. Xóa object, version và bucket S3 của TechBlog.
6. Xóa secret hoặc parameter, IAM policy/role riêng của workshop.
7. Xóa CloudWatch alarm, log group, dashboard và SNS topic/subscription.
8. Xóa Security Group sau khi mọi network interface phụ thuộc đã biến mất.
9. Giữ hoặc xóa AWS Budget theo kế hoạch sử dụng tài khoản.

## Xác nhận cuối

Mở Resource Explorer hoặc Resource Groups theo tag `Project=TechBlog`, kiểm tra từng Region đã sử dụng, sau đó xem Billing và Cost Explorer. Đặc biệt kiểm tra snapshot RDS, volume EBS, Elastic IP, object version trong S3, log và tài nguyên CloudFront/WAF còn sót.

Hoàn tất workshop khi TechBlog đã được kiểm thử, bằng chứng đã lưu và không còn tài nguyên demo ngoài ý muốn.
