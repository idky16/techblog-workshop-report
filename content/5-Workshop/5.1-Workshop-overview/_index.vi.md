---
title: "Tổng quan workshop"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

{{% notice info %}}
### Truy cập bản demo trực tiếp

Mentor có thể truy cập website TechBlog đang chạy trên AWS tại:

[http://13.212.52.148:8080](http://13.212.52.148:8080)

Website được phục vụ trực tiếp bởi Spring Boot trên Amazon EC2 qua HTTP port 8080. Bản demo chưa sử dụng domain hoặc HTTPS và dự kiến được duy trì đến ngày 30/07/2026.
{{% /notice %}}

## Tổng quan workshop

Workshop này giải thích cách nhóm TTV triển khai TechBlog từ IntelliJ/Laragon local lên AWS mà vẫn giữ Spring Boot MVC monolith. Thymeleaf page, controller, security configuration, business service, repository và persistence logic tiếp tục nằm trong một executable JAR. Workshop không thiết kế lại hệ thống thành React SPA, API Gateway application hoặc frontend/backend tách riêng.

Môi trường học tập sử dụng một EC2 public cho ứng dụng và một RDS MySQL private cho relational data. S3 lưu object avatar và ảnh bài viết, Amazon SES gửi admin registration notification, còn CloudWatch tập trung application log và theo dõi CPU alarm. Người dùng tạm thời truy cập website qua:

```text
http://<EC2_PUBLIC_IP>:8080/
```

AWS Budgets được tạo trước các tài nguyên có tính phí. EC2 dùng `techblog-ec2-role` cho AWS SDK và CloudWatch Agent; project không lưu static AWS credential.

## Kết quả mục tiêu

| Kết quả | Trạng thái có thể kiểm tra |
|---|---|
| EC2 runtime | `techblog-ec2` chạy Amazon Linux 2023 và Java 17 Amazon Corretto. |
| Application process | Executable JAR chạy bằng `techblog.service` và vẫn active sau khi đóng SSH. |
| Database | `techblog-db` private lưu database `techblog` cùng các bảng đã import. |
| Public access | `curl -I http://localhost:8080` và trình duyệt trả HTTP 200. |
| Phân quyền | Thực hiện các kịch bản Guest, USER/Reader, EDITOR/Writer và ADMIN. |
| Object storage | Avatar và ảnh bài viết dùng `avatars/`, `posts/` trong bucket private `techblog-uploads-ttv-2026`. |
| Database reference | MySQL lưu `s3:avatars/<uuid>.<ext>` hoặc `s3:posts/<uuid>.<ext>`, không lưu binary ảnh hoặc presigned URL. |
| Hiển thị ảnh private | Ảnh S3 mới dùng presigned GET URL có thời hạn 10 phút; ảnh local legacy vẫn hoạt động. |
| Transactional email | Sau khi registration được lưu, Amazon SES gửi best-effort notification đến admin recipient đã verify và được cấu hình. |
| Centralized logs | CloudWatch Agent gửi event phù hợp từ `/var/log/techblog/application.log` đến Standard-class `/techblog/application` với retention 14 ngày. |
| Alarm | `techblog-high-cpu` theo dõi Average CPUUtilization lớn hơn 70% trong 2/2 period, mỗi period 5 phút và không có notification action. |
| Network boundary | RDS port `3306` chỉ nhận EC2 Security Group. |
| Kiểm tra upload | Test regression avatar 2,09 MB thành công. Giới hạn multipart của Spring là 50 MB cho mỗi file/request, còn application giới hạn avatar 5 MiB và ảnh bài viết 15 MiB. |

Console state, object key, log-stream name, email delivery và alarm state thực tế phải được chứng minh bằng bằng chứng đã che thông tin nhạy cảm; trang này không tự tạo các giá trị đó.

## Kiến trúc đã triển khai

Đường liền thể hiện request, database, object, email và log path. Quan hệ IAM dùng đường nét đứt vì đây là cơ chế cấp credential, không phải đường truyền application data. Budgets được đặt ngoài runtime request path.

{{< mermaid align="center" >}}
flowchart LR
    U[Reader / Writer / Admin<br/>Trình duyệt] -->|HTTP :8080| G[Internet Gateway]
    subgraph VPC[Amazon VPC]
        subgraph PUB[Public Subnet]
            E[Amazon EC2<br/>techblog-ec2<br/>Spring Boot + systemd]
        end
        subgraph DBNET[Đường database private]
            R[(Amazon RDS for MySQL<br/>techblog-db<br/>techblog)]
        end
        G --> E
        E -->|JDBC :3306| R
    end
    E -. instance profile .-> I[AWS IAM Role<br/>techblog-ec2-role]
    E -->|AWS SDK<br/>put / get / delete| S[(Amazon S3<br/>avatars/ và posts/)]
    E -->|AWS SDK<br/>thông báo đăng ký cho admin| M[Amazon SES<br/>Sandbox]
    E -->|CloudWatch Agent<br/>application.log| L[CloudWatch Logs<br/>/techblog/application]
    E -->|CPUUtilization| A[CloudWatch Alarm<br/>techblog-high-cpu]
    B[AWS Budgets] --> O[Cảnh báo chi phí shared account]
{{< /mermaid >}}

Cách đọc sơ đồ:

- Luồng chính là `User → Internet Gateway → EC2 Spring Boot → RDS MySQL`.
- S3, SES, CloudWatch, IAM và Budgets là dịch vụ AWS nằm ngoài phần subnet EC2/RDS.
- S3 bucket private không phải static website hosting. Spring Boot dùng AWS SDK để thao tác object.
- Media resolver đã triển khai tạo presigned GET URL 10 phút cho reference `s3:` và giữ nguyên URL của ảnh local legacy.
- Upload validator kiểm tra signature JPEG, PNG, GIF hoặc WebP; SVG và file không phải ảnh nhưng giả extension bị từ chối.
- Lỗi Amazon SES phải được xử lý ngoài database registration transaction để lỗi email tạm thời không rollback user hợp lệ.
- CloudWatch alarm được theo dõi trong CloudWatch console. Báo cáo không tuyên bố SNS hoặc SES gửi alarm notification.

## Luồng triển khai workshop

| Bước | Hoạt động | Điểm kiểm tra |
|---:|---|---|
| 1 | Kiểm tra Java, Maven Wrapper, MySQL, upload và role ở local | TechBlog chạy tại `http://localhost:8080`. |
| 2 | Tạo AWS Budgets | Có cost alert trước khi provision tài nguyên. |
| 3 | Tạo và bảo vệ S3 | Xác nhận bucket private, encryption và hai prefix. |
| 4 | Tạo và chuẩn bị IAM Role | Trust relationship và policy đã có; chưa gắn vào instance hoặc chạy runtime test. |
| 5 | Xác minh Amazon SES identity | Sender identity đã verify và trạng thái Sandbox được ghi lại. |
| 6 | Tạo RDS MySQL | `techblog-db` đạt `Available`, public access disabled. |
| 7 | Chuẩn bị EC2-RDS Security Group và dữ liệu migration local | RDS `3306` tham chiếu EC2 SG đã chuẩn bị, connection placeholder an toàn được ghi nhận và `techblog.sql` tồn tại ở local. |
| 8 | Launch EC2, gắn IAM Role và cài runtime tool | Amazon Linux 2023 có `techblog-ec2-role`, Java 17, MariaDB client và CloudWatch Agent. |
| 9 | Kết nối RDS và import database | Chín bảng TechBlog hiển thị trên RDS. |
| 10 | Build và upload JAR | `techblog-0.0.1-SNAPSHOT.jar` được copy lên EC2. |
| 11 | Cấu hình RDS, S3 provider/bucket/TTL, SES sender/admin recipient và log | Environment file giới hạn quyền chứa các giá trị được resolve trên EC2. |
| 12 | Chạy `systemd` | Service active và local HTTP check trả 200. |
| 13 | Cấu hình CloudWatch Logs và CPU alarm | Log event mới và definition của `techblog-high-cpu` hiển thị trên console. |
| 14 | Kiểm thử role, S3, SES và upload limit | Thu bằng chứng chức năng mà không lộ secret. |
| 15 | Review security và troubleshooting | Các lỗi triển khai đã có nguyên nhân và cách xử lý. |
| 16 | Dọn dẹp | Tài nguyên có tính phí được xóa hoặc giữ lại có chủ đích. |

## Dịch vụ AWS trong phạm vi

| Dịch vụ | Trạng thái trong workshop | Mục đích |
|---|---|---|
| AWS Budgets | Đã cấu hình | Kiểm soát chi phí cho shared environment của nhóm TTV. |
| Amazon EC2 | Đã triển khai | Chạy Spring Boot MVC JAR và CloudWatch Agent. |
| Amazon RDS for MySQL | Đã triển khai private | Lưu relational data của TechBlog. |
| Amazon S3 | Application storage đã tích hợp | Lưu object avatar và ảnh bài viết trong bucket private. |
| AWS IAM Role | Đã gắn EC2 | Dùng `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` và inline `TechBlogSesSendEmail`; không dùng static key. |
| Amazon SES | Đã tích hợp trong Sandbox | Gửi admin registration notification từ verified email identity; lỗi gửi không rollback registration. |
| Amazon CloudWatch Logs | Đã triển khai | Lưu application event trong Standard-class `/techblog/application` trong 14 ngày. |
| Amazon CloudWatch Alarm | Đã triển khai, không có action | `techblog-high-cpu` giám sát CPU cao kéo dài và được theo dõi trên console. |

## Bước 1: Quan sát kiến trúc triển khai

Theo dõi một browser request đến EC2, một JDBC request đến RDS, một image operation đến S3, một registration-email call đến SES và một application-log event đến CloudWatch. Cách trình bày này giúp sơ đồ có thể defend vì mỗi mũi tên đều gắn với một bước kiểm tra trong workshop.

<figure>
  {{< content-image src="images/5-Workshop/Techblog_Workflow_AWS.png" alt="Workflow TechBlog đã triển khai trên AWS" >}}
  <figcaption><em>Hình 5.1.1. Workflow TechBlog kết nối EC2, RDS, S3, Amazon SES, CloudWatch và EC2 IAM Role.</em></figcaption>
</figure>

## Bước 2: Xác định các dịch vụ AWS được sử dụng

Mở Resource Groups hoặc console view mà nhóm đã dùng và chỉ ghi nhận tài nguyên thuộc TechBlog. Che account ID, email, public IP, endpoint, cookie và credential trước khi đưa ảnh vào báo cáo.

<!-- TODO: Thêm /images/5-Workshop/5.1-Workshop-overview/aws-services-overview.png. Caption: *Hình 5.1.2. Các dịch vụ AWS được sử dụng trong workshop TechBlog.* -->

## Giới hạn của môi trường demo

- Website dùng HTTP qua public IP và port `8080`; chưa có HTTPS hoặc domain.
- EC2 public IP có thể đổi sau stop/start.
- EC2 và RDS dùng cấu hình nhỏ, Single-AZ cho môi trường học tập.
- Không có Nginx, Route 53, ALB, Auto Scaling, Multi-AZ, CloudFront, WAF hoặc NAT Gateway.
- S3 bucket private; ảnh hiển thị qua presigned GET URL 10 phút và ảnh local legacy tiếp tục được hỗ trợ.
- SES vẫn ở Sandbox nên admin recipient được cấu hình phải verify; TechBlog không gửi notification này đến mọi email đăng ký.
- `techblog-high-cpu` được theo dõi trên console vì workshop chưa dùng SNS notification.
- Database credential nằm trong environment file mode `600` cho demo; Secrets Manager hoặc Parameter Store là hướng phát triển.
- Đây là môi trường học tập/demo, không được mô tả là production-ready.

## Phân công nhóm

| Thành viên | Trách nhiệm chính | Deliverable |
|---|---|---|
| Thành viên 1 | AWS Budgets, IAM Role, S3 infrastructure và kiểm thử S3 | Bằng chứng Budget, bucket setting, review policy hiện tại, hướng thu hẹp quyền S3 và object lifecycle test. |
| Thành viên 2 | RDS MySQL, Security Group, migration và data verification | RDS private, SG reference, database import, kiểm tra chín bảng và S3-key reference. |
| Thành viên 3 | EC2, Java, runtime configuration, systemd và SES | Runtime deployment, environment value an toàn, service evidence, SES identity, CLI send và admin-notification test. |
| Thành viên 4 | Functional testing, CloudWatch, security và troubleshooting | Role matrix, centralized log, CPU alarm, HTTP 413 regression, security review và bảng lỗi. |

Phần dùng chung:

- Cả bốn thành viên cùng duyệt 5.1 và 5.2.
- Cả bốn thành viên thống nhất phần 2-Proposal.
- Cả bốn thành viên thực hiện checklist cleanup ở 5.7.
- Nhóm dùng một AWS account, không chia sẻ root credentials và tạo identity least privilege riêng nếu nhiều thành viên cần truy cập console.

## Kết quả mong đợi

Người đọc có thể giải thích toàn bộ TechBlog deployment, phân biệt main request path với AWS integration, xác định security boundary của từng dịch vụ và tiếp tục các module sau mà không phụ thuộc vào kiến trúc của project mẫu không liên quan.
