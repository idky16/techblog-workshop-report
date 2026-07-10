---
title: "Tổng quan workshop"
date: 2026-07-10
weight: 1
pre: " <b> 5.1. </b> "
---

## Mục tiêu

Sau workshop, bạn có thể:

- Chuyển TechBlog từ `localhost:8080` và MySQL local sang AWS.
- Triển khai file JAR Spring Boot trên EC2 và kết nối RDS MySQL.
- Chuyển `storage/avatars` và `storage/posts` sang S3.
- Dùng IAM Role và secret thay cho credential ghi trong source code.
- Kiểm tra luồng Reader, Writer và Admin trên môi trường AWS.
- Thu thập log bằng CloudWatch, gửi alarm bằng SNS và kiểm soát chi phí bằng Budgets.
- Đặt CloudFront và AWS WAF trước EC2 sau khi kiểm tra endpoint trực tiếp.

## Phạm vi bản demo

TechBlog giữ nguyên kiến trúc monolithic MVC. Giao diện Thymeleaf và logic nghiệp vụ cùng chạy trong một ứng dụng Spring Boot; workshop không chuyển sang React SPA, API Gateway hay Elastic Beanstalk.

Luồng triển khai là **User → CloudFront + WAF → EC2 Spring Boot → RDS MySQL + S3**. RDS chỉ nhận kết nối cổng 3306 từ Security Group của EC2. Bucket upload bật Block Public Access; EC2 truy cập S3 qua IAM Role.

## Kết quả cần đạt

- Website truy cập được trên endpoint demo.
- Dữ liệu nghiệp vụ được ghi vào RDS.
- Avatar và ảnh bài viết được lưu trong S3.
- Không có mật khẩu database hoặc Access Key trong repository.
- CloudWatch có log ứng dụng và SNS nhận được cảnh báo thử nghiệm.
