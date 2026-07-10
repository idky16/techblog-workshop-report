---
title: "Điều kiện tiên quyết"
date: 2026-07-10
weight: 2
pre: " <b> 5.2. </b> "
---

## Tài khoản và công cụ

- AWS account có quyền tạo Budgets, EC2, RDS, S3, IAM, Secrets Manager hoặc SSM Parameter Store, CloudWatch, SNS, CloudFront và WAF.
- Git, Java 17 và Maven Wrapper hoạt động trên máy phát triển.
- Source TechBlog build và chạy được với MySQL local.
- Có quyền truy cập AWS Management Console và một địa chỉ email để xác nhận SNS.

## Kiểm tra ứng dụng local

1. Chạy `./mvnw clean package` hoặc `mvnw.cmd clean package` trên Windows.
2. Khởi động file JAR và truy cập `http://localhost:8080`.
3. Kiểm tra đăng ký, đăng nhập, phân quyền Reader/Writer/Admin, tạo bài, duyệt bài, comment và upload ảnh.
4. Xác định các cấu hình cần chuyển thành biến môi trường: `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`, `AWS_REGION` và `S3_BUCKET`.
5. Bảo đảm credential thật không nằm trong Git.

## Quy ước tài nguyên

Dùng một Region cho toàn workshop và gắn tag `Project=TechBlog`. Chọn tên duy nhất cho S3 bucket, đặt retention log ngắn cho demo và tạo AWS Budget trước khi dựng hạ tầng.

{{% notice warning %}}
Không sử dụng root account cho công việc hằng ngày và không commit Access Key hoặc mật khẩu database.
{{% /notice %}}
