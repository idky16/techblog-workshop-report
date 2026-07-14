---
title: "Workshop triển khai TechBlog trên AWS"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

{{% notice info %}}
### Truy cập bản demo trực tiếp



[http://13.212.52.148:8080](http://13.212.52.148:8080)



## Tổng quan workshop

Workshop thực hành này ghi lại cách nhóm TTV chuyển TechBlog từ IntelliJ, Laragon, MySQL local và thư mục upload local lên một môi trường demo AWS tại `ap-southeast-1`. TechBlog giữ nguyên kiến trúc monolithic MVC với Java 17 và Spring Boot 3.5.12: Thymeleaf render giao diện, Spring Security kiểm soát USER/EDITOR/ADMIN, Spring Data JPA/Hibernate làm việc với MySQL và Maven Wrapper tạo một executable JAR.

Workshop trình bày toàn bộ như một quy trình triển khai thống nhất. Nhóm thiết lập cost control, S3 object storage, EC2 IAM Role, Amazon SES identity, RDS MySQL private, EC2 runtime configuration, CloudWatch Logs, một CloudWatch CPU alarm, kiểm thử chức năng và cleanup. Ứng dụng được truy cập trực tiếp trong demo tại:

```text
http://<EC2_PUBLIC_IP>:8080/
```

Không bước nào yêu cầu lưu static AWS Access Key hoặc Secret Access Key trong source code.

## Tóm tắt kiến trúc

Luồng request chính là `User → Internet Gateway → EC2 Spring Boot → RDS MySQL`. Cùng EC2 instance đó dùng `techblog-ec2-role` để gọi S3 cho avatar/ảnh bài viết, Amazon SES cho admin notification và CloudWatch cho centralized log cùng EC2 CPU alarm.

- `techblog-ec2`: Amazon Linux 2023, Java 17 Amazon Corretto, `t3.micro`, 8 GiB gp3.
- `techblog-db`: Amazon RDS for MySQL private, `db.t3.micro`, Single-AZ, port `3306`.
- `techblog-uploads-ttv-2026`: S3 bucket private có `avatars/` và `posts/`.
- `techblog.service`: `systemd` unit chạy Spring Boot JAR.
- `/techblog/application`: Standard-class CloudWatch Log Group với retention 14 ngày và stream name là EC2 instance ID.
- `techblog-high-cpu`: alarm Average CPUUtilization lớn hơn 70% trong 2/2 period, mỗi period 5 phút và không có notification action.
- Amazon SES: verified email identity trong Sandbox để gửi admin notification sau khi đăng ký thành công.

HTTPS, Nginx, Route 53, CloudFront, AWS WAF, ALB, Auto Scaling, Multi-AZ, API Gateway, Amplify, Elastic Beanstalk và NAT Gateway không thuộc kiến trúc demo hiện tại.

## Dịch vụ AWS sử dụng

| Dịch vụ | Mục đích trong workshop |
|---|---|
| AWS Budgets | Cảnh báo chi phí trên shared account của nhóm. |
| Amazon EC2 | Chạy Spring Boot MVC JAR. |
| Amazon RDS for MySQL | Lưu private database `techblog`. |
| Amazon S3 | Lưu object avatar và ảnh bài viết trong bucket private. |
| AWS IAM | Cấp temporary credential cho EC2 qua các policy hiện tại `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` và `TechBlogSesSendEmail`. |
| Amazon SES | Gửi best-effort registration notification đến admin recipient đã verify trong khi SES còn ở Sandbox. |
| Amazon CloudWatch Logs | Tập trung Spring Boot log không chứa dữ liệu nhạy cảm. |
| Amazon CloudWatch Alarm | Theo dõi CPU EC2 cao kéo dài; không cấu hình SNS notification action. |

## Nội dung workshop

1. [Tổng quan workshop](5.1-workshop-overview/)
2. [Chuẩn bị](5.2-prerequisites/)
3. [Dịch vụ nền tảng](5.3-budget-s3-iam/)
   - [Tạo AWS Budget](5.3-budget-s3-iam/5.3.1-budget/)
   - [Tạo và bảo vệ S3 bucket](5.3-budget-s3-iam/5.3.2-s3/)
   - [Tạo và chuẩn bị EC2 IAM Role](5.3-budget-s3-iam/5.3.3-iam-role/)
   - [Cấu hình Amazon SES verified identity](5.3-budget-s3-iam/5.3.4-ses/)
   - [Kiểm tra Budget, S3, IAM và SES](5.3-budget-s3-iam/5.3.5-verify/)
4. [Chuẩn bị RDS MySQL](5.4-rds-mysql/)
   - Tạo RDS private, chuẩn bị ranh giới Security Group EC2–RDS, ghi giá trị kết nối và export database local.
5. [Triển khai Spring Boot lên EC2](5.5-deploy-ec2/)
   - Tạo EC2, gắn IAM Role, cài Java, kết nối/import RDS, upload JAR, cấu hình runtime RDS/S3/SES và chạy `systemd`.
6. [Kiểm thử, monitoring và vận hành](5.6-testing-operations/)
   - Kiểm thử role, S3 upload và HTTP 413, SES qua console/CLI/application, local log, CloudWatch Logs, CPU alarm, security và troubleshooting.
7. [Dọn dẹp tài nguyên](5.7-cleanup/)

## Kết quả mong đợi

Sau workshop, TechBlog chạy như một Spring Boot service trên EC2, dùng RDS MySQL private cho relational data, lưu avatar và ảnh bài viết mới trong S3 private với presigned GET URL 10 phút, gửi best-effort registration notification cho quản trị viên qua SES và cung cấp bằng chứng vận hành trong CloudWatch Logs cùng `techblog-high-cpu`. Screenshot còn thiếu chỉ được theo dõi bằng comment ẩn cho đến khi nhóm bổ sung bằng chứng thật đã che thông tin nhạy cảm.
