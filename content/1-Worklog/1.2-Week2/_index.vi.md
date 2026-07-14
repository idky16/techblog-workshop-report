---
title: "Tuần 2"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

## Thời gian

27/04/2026 - 03/05/2026

## Mục tiêu trong tuần

- Hiểu source TechBlog hiện tại trước khi đề xuất thay đổi lên AWS.
- Xác nhận application và database local là baseline ổn định để triển khai.

## Công việc đã thực hiện

- Rà soát stack Java 17, Spring Boot 3.5.12, Spring MVC, Spring Security, Spring Data JPA/Hibernate, Thymeleaf, MySQL và Maven Wrapper.
- Xác nhận frontend và backend được đóng gói chung trong một MVC monolith, không phải React application tách riêng.
- Chạy TechBlog tại `http://localhost:8080` và kiểm tra database `techblog` trên Laragon/MySQL.
- Xác định hai thư mục upload legacy `storage/avatars`, `storage/posts` và cách Thymeleaf hiển thị các file này.

## Kết quả và bài học

- Hoàn thành inventory về application, database, upload và runtime dependency.
- Xác nhận Amazon EC2 phù hợp để chạy executable JAR của ứng dụng monolithic.
- Xác định yêu cầu tương thích: ảnh local cũ vẫn phải hiển thị sau khi tích hợp S3.
