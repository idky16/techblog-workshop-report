---
title: "Dọn dẹp tài nguyên"
date: 2026-07-10
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Mục tiêu

Xóa hoặc chủ động giữ lại mọi tài nguyên AWS của TechBlog sau khi đã thu bằng chứng cuối. Cleanup bảo vệ shared account và credit của nhóm TTV khỏi tiếp tục phát sinh chi phí demo.

## Điều kiện trước khi thực hiện

- Screenshot và command output cần thiết đã được lưu và che thông tin nhạy cảm.
- Nhóm đã quyết định có giữ RDS data hoặc final snapshot không.
- Cả bốn thành viên xác nhận final demo đã hoàn tất.

## Thứ tự dọn dẹp

Thực hiện theo dependency thay vì xóa ngẫu nhiên:

1. Dừng application activity và lưu final evidence.
2. Terminate EC2 và kiểm tra EBS.
3. Xóa RDS sau khi quyết định snapshot.
4. Empty và delete S3.
5. Xóa CloudWatch Logs, CPU alarm và SES identity nếu không còn dùng.
6. Detach/delete IAM policy và role.
7. Xóa Security Group không còn dùng.
8. Review Budgets, Billing và mọi Region.

## Các bước thực hiện

### 1. EC2 và EBS

- Stop `techblog.service` nếu instance còn running.
- Stop EC2 nếu chỉ tạm nghỉ ngắn có kế hoạch hoặc terminate `techblog-ec2` sau final demo.
- Xác nhận root EBS volume có delete-on-termination setting đúng ý định.
- Xóa EBS volume hoặc snapshot unattached mà nhóm không cần.
- Kiểm tra Elastic IP không dùng dù workshop không yêu cầu tạo.

### 2. RDS và snapshot

- Chỉ stop `techblog-db` khi tạm nghỉ; stop không phải permanent cleanup.
- Trước khi delete, quyết định có tạo final snapshot theo yêu cầu report không.
- Nếu không cần final snapshot, chọn đúng delete option.
- Xóa manual snapshot cũ sau khi xác nhận không còn nhu cầu recovery.

### 3. S3 object và bucket

- Xóa test object trong `avatars/` và `posts/`.
- Kiểm tra incomplete multipart upload hoặc versioned object nếu setting từng thay đổi khi test.
- Empty `techblog-uploads-ttv-2026`, sau đó delete bucket.
- Không chuyển bucket thành public chỉ để cleanup dễ hơn.

### 4. Amazon SES

- Chỉ xóa verified sender identity nếu nhóm không tiếp tục sử dụng.
- Xác nhận không còn test configuration có thể tiếp tục gửi.
- Workshop không được có Dedicated IP, Mail Manager, Global Endpoint hoặc attachment resource.

### 5. CloudWatch

- Stop/uninstall CloudWatch Agent nếu EC2 được giữ cho mục đích khác.
- Xóa Log Group `/techblog/application` khi không còn cần evidence và log.
- Xóa `techblog-high-cpu`.
- Review log group, alarm hoặc dashboard được tạo thêm ngoài ý muốn.

### 6. IAM và Security Group

- Detach instance profile, `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` và inline `TechBlogSesSendEmail` sau khi EC2 không còn dùng.
- Chỉ xóa `techblog-ec2-role` sau khi hết dependency.
- Xóa EC2/RDS Security Group không còn dùng; giữ default Security Group.
- Xác nhận ứng dụng không từng tạo access key.

### 7. Review Budgets và Billing

- Giữ AWS Budgets khi project/account còn hoạt động hoặc xóa sau workshop nếu không cần.
- Review Billing/Cost Explorer sau cleanup.
- Kiểm tra `ap-southeast-1` và Region khác xem có tài nguyên tạo nhầm không.

## Checklist cuối

- [ ] EC2 đã terminate hoặc stop có chủ đích.
- [ ] Không còn EBS volume hoặc Elastic IP ngoài ý muốn.
- [ ] RDS đã delete/stop theo kế hoạch.
- [ ] Final snapshot decision đã được ghi.
- [ ] S3 object và bucket đã xóa khi không còn dùng.
- [ ] SES identity đã xóa hoặc giữ lại có chủ đích.
- [ ] CloudWatch Agent, Log Group và `techblog-high-cpu` đã xóa khi không còn dùng.
- [ ] IAM policy/role và custom Security Group đã cleanup.
- [ ] AWS Budgets và Billing đã được review.
- [ ] Region khác không có tài nguyên tạo nhầm.

## Kết quả mong đợi

Shared AWS account không còn chargeable TechBlog resource ngoài ý muốn. Mọi tài nguyên giữ lại đều có người phụ trách, mục đích và ngày dự kiến xóa.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Không delete được S3 bucket | Empty toàn bộ object, version và incomplete multipart upload. |
| Security Group đang in use | Xóa dependency EC2/RDS/network interface trước. |
| Không delete được IAM Role | Detach instance profile và managed/customer policy. |
| RDS deletion bị chặn | Review deletion protection và final-snapshot requirement cẩn thận. |
| Vẫn thấy chi phí sau cleanup | Kiểm tra billing delay, snapshot, EBS, Elastic IP, log và Region khác. |

## Ghi chú chi phí và bảo mật

Xóa bằng chứng quá sớm sẽ ảnh hưởng report; giữ tài nguyên vô thời hạn sẽ phát sinh chi phí. Lưu evidence đã che thông tin trước, sau đó cleanup theo dependency.

<!-- TODO: Thêm /images/5-Workshop/5.7-Cleanup/cleanup-checklist.png. Caption: *Hình 5.7.1. Cleanup TechBlog và review Billing cuối.* -->
