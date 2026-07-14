---
title: "Dịch vụ nền tảng"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
alwaysopen: true
---

## Mục tiêu module

Tạo cost guardrail cho shared account và các AWS integration cần có trước khi deploy ứng dụng: S3 object storage private, EC2 IAM Role và Amazon SES verified sender identity. Các dịch vụ này thuộc cùng kế hoạch triển khai TechBlog, không phải kiến trúc mẫu tách rời.

## Luồng module

{{< mermaid align="center" >}}
flowchart TB
    B[AWS Budgets<br/>cost guardrail]
    S[(Amazon S3<br/>bucket private)]
    I[AWS IAM Role<br/>chuẩn bị role và policy]
    M[Amazon SES<br/>verified email identity]
    I -. gắn vào instance ở 5.5 .-> E[Amazon EC2<br/>chưa tạo trong module này]
{{< /mermaid >}}

## Trang con

1. [Tạo AWS Budget](5.3.1-budget/)
2. [Tạo và bảo vệ S3 bucket](5.3.2-s3/)
3. [Tạo và chuẩn bị EC2 IAM Role](5.3.3-iam-role/)
4. [Cấu hình Amazon SES verified identity](5.3.4-ses/)
5. [Kiểm tra Budget, S3, IAM và SES](5.3.5-verify/)

## Cấu hình module

| Hạng mục | Giá trị bắt buộc |
|---|---|
| Region | `ap-southeast-1` |
| S3 bucket | `techblog-uploads-ttv-2026` |
| S3 access | Private, Block Public Access bật, ACLs disabled |
| S3 encryption | SSE-S3 |
| S3 prefixes | `avatars/`, `posts/` |
| IAM Role | `techblog-ec2-role` trusted by EC2 |
| Attached policy hiện tại | `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` |
| SES inline policy | `TechBlogSesSendEmail` |
| SES sender | `<SES_VERIFIED_SENDER_EMAIL>` |
| Trạng thái SES | Sandbox; chỉ gửi đến verified recipient |
| Static credentials | Không sử dụng |

## Kết quả mong đợi

Budget alert active, private bucket và prefix tồn tại, các IAM policy thật được ghi đúng và SES email identity đã verify trong Sandbox. `AmazonS3FullAccess` được ghi rõ là rộng hơn nhu cầu, không mô tả sai thành least privilege. Account ID, email và ARN thật được che trong report.

## Ghi chú chi phí và bảo mật

AWS Budgets chỉ cảnh báo, không tự dừng tài nguyên. S3 không bật Transfer Acceleration hoặc static website. SES không dùng Dedicated IP, Mail Manager, Global Endpoint hoặc attachment workflow. IAM Role là nguồn credential của ứng dụng.
