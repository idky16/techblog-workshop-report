---
title: "Review bảo mật và troubleshooting"
date: 2026-07-10
weight: 7
chapter: false
pre: " <b> 5.6.7. </b> "
---

## Mục tiêu

Hoàn thành security review cuối và tổng hợp lỗi triển khai thật cùng các kiểm tra S3, SES, CloudWatch mới.

## Điều kiện trước khi thực hiện

- Đã review EC2, RDS, S3, SES, `systemd`, CloudWatch Logs và alarm configuration.
- Functional/integration test đã có pass/fail result.

## Các bước thực hiện

Review từng boundary:

1. **AWS account:** không chia sẻ hoặc dùng root credentials hằng ngày; member dùng identity least privilege riêng khi cần.
2. **EC2:** SSH `22` chỉ nhận `<MY_PUBLIC_IP>`; TCP `8080` chỉ public trong demo window.
3. **RDS:** public access disabled; `3306` chỉ nhận EC2 Security Group.
4. **S3:** Block Public Access bật, ACLs disabled và ứng dụng không tạo permanent public object URL.
5. **IAM:** đã gắn `techblog-ec2-role`; không có static key. `CloudWatchAgentServerPolicy` và `TechBlogSesSendEmail` hỗ trợ đúng chức năng đã xác minh, còn `AmazonS3FullAccess` hiện tại được đánh dấu cần thu hẹp trong tương lai.
6. **SES:** tuân thủ verified email identity và Sandbox rule; admin notification không có password, hash, JWT hoặc secret.
7. **Logs:** local/CloudWatch log không chứa password, token, cookie, authorization header và sensitive user content.
8. **Runtime secrets:** `/etc/techblog/techblog.env` ở mode `600`, không đưa vào Git/screenshot.
9. **Giới hạn demo:** truy cập bằng HTTP, không có Nginx, HTTPS, domain, CloudFront, WAF, ALB hoặc Auto Scaling.

## Bảng troubleshooting

| Lỗi | Nguyên nhân thường gặp | Cách xử lý |
|---|---|---|
| SSH timeout | Public IP người dùng thay đổi | Cập nhật SSH inbound source thành `<MY_PUBLIC_IP>`. |
| PEM bad permissions | Windows OpenSSH từ chối ACL quá rộng | Chạy các lệnh `icacls` đã ghi. |
| Unknown MySQL server host | Sai một ký tự trong RDS endpoint | Copy lại và so sánh `<RDS_ENDPOINT>`. |
| RDS Access denied | Sai database password | Nhập lại mà không lưu trong report hoặc shell history. |
| Communications link failure | Sai RDS SG/VPC path hoặc endpoint | Kiểm tra RDS status, port `3306`, EC2 SG reference và endpoint. |
| Public website không truy cập được | Port `8080` đóng hoặc public IP đổi | Kiểm tra EC2 SG, service state và address hiện tại. |
| Lệnh `java -jar` bị tách | Backslash/dòng trống làm hỏng command | Giữ `ExecStart` trên một dòng. |
| Kiểm tra quá sớm | Spring Boot vẫn đang start | Chờ và đọc `journalctl`. |
| HTTP 413 | JAR đang chạy có multipart transport limit thấp hơn request hoặc upstream layer từ chối request | Xác nhận setting 50 MB cho file/request, rebuild, thay JAR và restart. Ảnh 2,09 MB chỉ là test regression, không phải giới hạn cấu hình. |
| Ảnh hợp lệ bị từ chối | Avatar vượt 5 MiB, ảnh bài viết vượt 15 MiB hoặc signature JPEG/PNG/GIF/WebP không hợp lệ | Dùng đúng giới hạn nghiệp vụ và file ảnh thật được hỗ trợ; không cho SVG hoặc chỉ tin extension. |
| S3 AccessDenied | Sai IAM object ARN/action/prefix | So sánh role policy với SDK request thật. |
| S3 image trả 403 | Presigned URL 10 phút đã hết hạn hoặc tạo sai key | Refresh trang, kiểm tra marker `s3:` và Region; giữ bucket private. |
| S3 object mồ côi | Replace/delete path không xóa key cũ | Review application lifecycle logic và database reference. |
| SES MessageRejected | Sandbox sender hoặc admin recipient chưa verify | Verify cả hai email identity trong `ap-southeast-1`. |
| SES lỗi làm rollback sign-up | Mail call nằm trong registration transaction | Gửi sau commit hoặc catch failure ngoài transaction. |
| Thiếu CloudWatch stream | Sai agent, file path, IAM permission hoặc Region | Kiểm tra local file, agent status/log, role và `ap-southeast-1`. |
| Alarm Insufficient data | Sai instance/Region hoặc chưa đủ period | Kiểm tra metric dimension và chờ data. |

## Cấu hình

| Hạng mục public | Trạng thái demo cho phép |
|---|---|
| EC2 SSH | `<MY_PUBLIC_IP>/32` |
| EC2 application | TCP `8080` public tạm thời |
| RDS MySQL | Chỉ EC2 Security Group |
| S3 | Không public access |
| Secrets | Không nằm trong Git, log hoặc screenshot |

## Cách kiểm tra

- Chạy lại `systemctl`, `journalctl`, `ss`, process và HTTP check.
- Review EC2 và RDS Security Group cạnh nhau.
- Review S3 permission và một object lifecycle result.
- Review SES identity/Sandbox state, console send, IAM Role CLI send và một admin notification đã che thông tin.
- Review CloudWatch Log Group class Standard, retention/event và definition `techblog-high-cpu`.
- Tìm credential pattern trong repository trước khi public report.

## Kết quả mong đợi

Public exposure, private data path, AWS permission, monitoring behavior và known error fix của deployment được ghi rõ và có thể defend.

## Ghi chú chi phí và bảo mật

Đóng hoặc giới hạn demo port `8080`, xóa test object/log không cần và stop chargeable resource sau khi lấy bằng chứng. HTTPS và managed secret storage vẫn là hướng phát triển.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/final-security-review.png. Caption: *Hình 5.6.7.1. Security review cuối của EC2, RDS, S3, IAM, SES và CloudWatch sau khi che thông tin.* -->
