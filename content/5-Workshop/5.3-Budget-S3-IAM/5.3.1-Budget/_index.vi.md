---
title: "Tạo AWS Budget"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Mục tiêu

Tạo cảnh báo ngân sách trước khi provision EC2, RDS và S3.

## Điều kiện trước khi thực hiện

- Dùng AWS account chung cho demo của nhóm TTV.
- Ưu tiên đăng nhập bằng IAM identity; root chỉ dùng cho billing hoặc recovery.

## Các bước thực hiện

1. Mở **Billing and Cost Management**.
2. Vào **Budgets**.
3. Tạo cost budget theo giới hạn demo trong tháng.
4. Cấu hình email notification cho chủ tài khoản hoặc thành viên phụ trách.
5. Review và tạo budget.

## Cấu hình

| Thiết lập | Giá trị |
|---|---|
| Budget type | Cost budget |
| Phạm vi | Shared TechBlog demo account |
| Ghi chú Region | Billing là global, nhưng tài nguyên chính nằm ở `ap-southeast-1`. |
| Alert | Email notification trước khi vượt giới hạn |

## Cách kiểm tra

Xác nhận budget xuất hiện trong AWS Budgets và email notification đúng.

## Kết quả mong đợi

AWS Budgets được cấu hình trước tài nguyên khác. Budget chỉ cảnh báo nhóm; dịch vụ này không tự động dừng EC2, RDS hoặc S3.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Email budget chưa xác nhận | Kiểm tra inbox và spam. |
| Tạo budget quá muộn | Tạo trước compute và database. |

## Ghi chú chi phí và bảo mật

Budget alert giúp giảm rủi ro chi phí bất ngờ nhưng không thay thế cleanup.

<!-- TODO: Thêm /images/5-Workshop/5.3-Budget-S3-IAM/aws-budget.png. Caption: *Hình 5.3.1.1. AWS Budget được tạo trước tài nguyên TechBlog.* -->
