---
title: "Tuần 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

## Thời gian

18/05/2026 - 24/05/2026

## Mục tiêu trong tuần

- Thiết lập cost control và foundation service trước khi tạo compute resource.
- Chuẩn bị media storage private và quyền AWS theo role, không dùng static key.

## Công việc đã thực hiện

- Tạo AWS Budget để theo dõi chi phí trước các tài nguyên chính có tính phí.
- Tạo bucket private `techblog-uploads-ttv-2026`, bật Block Public Access, SSE-S3 và tạo prefix `avatars/`, `posts/`.
- Tạo `techblog-ec2-role`, chuẩn bị `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` và inline `TechBlogSesSendEmail` theo trạng thái demo đã xác minh.
- Verify một Amazon SES email identity tại `ap-southeast-1` và ghi nhận giới hạn Sandbox.

## Kết quả và bài học

- Xác nhận source code không cần Access Key hoặc Secret Access Key.
- Hoàn thành ranh giới S3 private và IAM cần cho application.
- Ghi rõ `AmazonS3FullAccess` là policy hiện tại của demo và nên thu hẹp theo bucket/prefix trong tương lai.
