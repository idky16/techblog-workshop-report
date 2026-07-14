---
title: "Nhật ký công việc"
date: 2026-04-17
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Nhật ký cá nhân này ghi lại hành trình học tập và triển khai trong 12 tuần xoay quanh project TechBlog của nhóm TTV. Nội dung bám đúng phạm vi kỹ thuật cuối cùng: Java 17, Spring Boot 3.5.12 MVC monolith chạy trên Amazon EC2, Amazon RDS for MySQL private, Amazon S3 private cho media, Amazon SES gửi thông báo đăng ký và Amazon CloudWatch phục vụ monitoring.

## Tổng quan 12 tuần

Phần giữa của nhật ký công việc được sắp xếp để thể hiện quá trình chuyển từ chuẩn bị hạ tầng sang tích hợp ứng dụng và giám sát cho bản demo TechBlog trên AWS.

| Tuần | Trọng tâm chính |
|---:|---|
| 1 | Làm quen chương trình, Cloud Computing và yêu cầu báo cáo |
| 2 | Khảo sát kiến trúc và môi trường local của TechBlog |
| 3 | Kiểm tra phân quyền, build và yêu cầu triển khai |
| 4 | Thiết kế kiến trúc AWS tiết kiệm chi phí |
| 5 | Kiểm soát chi phí, lưu trữ S3 private, IAM Role và Amazon SES identity |
| 6 | RDS MySQL private, Security Group và export database local |
| 7 | Launch EC2, import database, Spring Boot JAR và systemd |
| 8 | Tích hợp S3, hỗ trợ ảnh legacy và kiểm tra upload |
| 9 | Tích hợp Amazon SES và admin registration notification |
| 10 | CloudWatch Agent, centralized log và CPU alarm |
| 11 | Kiểm thử end-to-end, review bảo mật và troubleshooting |
| 12 | Hoàn thiện báo cáo Hugo song ngữ, sơ đồ và demo cuối |

Các trang từng tuần chỉ ghi những dịch vụ và công việc liên quan trực tiếp đến môi trường TechBlog đã triển khai. Dịch vụ chưa có trong demo không được trình bày như kết quả đã hoàn thành.
