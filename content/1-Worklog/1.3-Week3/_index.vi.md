---
title: "Tuần 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

## Thời gian

04/05/2026 - 10/05/2026

## Mục tiêu trong tuần

- Kiểm tra nghiệp vụ chính và ranh giới phân quyền của TechBlog trên local.
- Chuyển yêu cầu chức năng thành checklist triển khai AWS.

## Công việc đã thực hiện

- Kiểm thử Guest và USER/Reader: đăng ký, đăng nhập, đọc bài, like, save, comment, reply, report, profile, avatar và Writer request.
- Kiểm thử quyền sở hữu bài của EDITOR/Writer cùng vòng đời `PENDING`, `APPROVED`, `REJECTED`.
- Rà soát luồng ADMIN quản lý user, category, comment, report, Writer request và duyệt bài.
- Chạy Maven Wrapper build và ghi nhận các environment value cần thiết mà không sao chép credential vào báo cáo.

## Kết quả và bài học

- Hoàn thành ma trận kiểm thử cho Guest, USER, EDITOR và ADMIN.
- Xác nhận việc chuyển lên AWS phải giữ authentication, authorization, database behavior và Thymeleaf server-rendered page.
- Xác định RDS, object storage private, process management, monitoring và cost control là yêu cầu hạ tầng chính.
