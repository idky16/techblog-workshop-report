---
title: "Triển khai TechBlog trên Amazon EC2"
date: 2026-07-10
weight: 4
pre: " <b> 5.4. </b> "
---

## Tạo IAM Role và EC2

1. Tạo EC2 IAM Role có quyền đọc đúng secret/parameter, ghi CloudWatch Logs và truy cập đúng bucket TechBlog.
2. Tạo EC2 Linux nhỏ nhưng đủ RAM cho JVM, cùng VPC với RDS và gắn `techblog-ec2-sg`.
3. Gắn IAM Role vào instance. Không tạo Access Key để đặt trên máy chủ.
4. Kết nối bằng Systems Manager Session Manager nếu có; nếu dùng SSH, giới hạn cổng 22 theo IP.

## Cài Java và triển khai JAR

Build ứng dụng trên máy phát triển:

```bash
./mvnw clean package -DskipTests
```

Chuyển file JAR lên EC2, cài Java 17 và cấu hình biến môi trường:

```bash
export DB_URL='jdbc:mysql://<rds-endpoint>:3306/techblog'
export DB_USERNAME='<database-user>'
export DB_PASSWORD='<read-from-secret-store>'
export AWS_REGION='<region>'
export S3_BUCKET='<techblog-bucket>'
java -jar techblog.jar
```

Trong triển khai thực tế, đọc secret khi khởi động và chạy ứng dụng bằng `systemd` để tự khởi động lại. Không ghi giá trị secret vào unit file công khai hoặc log.

## Kiểm tra endpoint trực tiếp

Truy cập `http://<ec2-public-ip>:8080` trong giai đoạn kiểm tra. Xác nhận ứng dụng khởi động, schema được tạo và đăng nhập hoạt động. Sau khi ổn định, chuyển sang endpoint CloudFront/WAF và thu hẹp inbound 8080 theo thiết kế phù hợp.
