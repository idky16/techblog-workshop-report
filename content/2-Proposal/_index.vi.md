---
title: "Đề xuất triển khai TechBlog trên AWS"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## Tóm tắt đề xuất

TechBlog là project kỹ thuật dùng chung của nhóm TTV. Đây là ứng dụng web blog công nghệ được xây dựng bằng Java 17, Spring Boot 3.5.12, Spring MVC, Spring Security, Spring Data JPA/Hibernate, Thymeleaf, MySQL và Maven Wrapper. Frontend và backend được đóng gói trong cùng Spring Boot executable JAR; TechBlog vẫn là ứng dụng monolithic MVC render giao diện phía server, không chuyển thành React SPA hoặc nền tảng API tách riêng.

Đề xuất này chuyển ứng dụng từ môi trường local IntelliJ/Laragon lên một môi trường demo AWS tiết kiệm chi phí tại Asia Pacific (Singapore), `ap-southeast-1`. Người dùng truy cập ứng dụng trực tiếp qua HTTP port `8080` trên Amazon EC2. Ứng dụng dùng Amazon RDS for MySQL private, lưu object avatar và ảnh bài viết trong Amazon S3 bucket private, gửi email giao dịch bằng Amazon SES, đồng thời đưa application log và metric vận hành lên Amazon CloudWatch. AWS IAM Role cấp temporary credential cho EC2, AWS Budgets kiểm soát chi phí nhóm và `systemd` duy trì Spring Boot process.

URL demo dùng placeholder vì public IP của EC2 có thể thay đổi:

```text
http://<EC2_PUBLIC_IP>:8080/
```

## Giới thiệu

Đề xuất ghi lại trạng thái AWS cuối cùng đã được xác minh, không trình bày kiến trúc dự kiến như kết quả đã triển khai. TechBlog chạy bằng `techblog.service` trên Amazon Linux 2023 với `WorkingDirectory=/home/ec2-user`. Ứng dụng dùng Amazon RDS for MySQL private cho dữ liệu nghiệp vụ, Amazon S3 private cho media mới, Amazon SES để thông báo đăng ký cho quản trị viên và Amazon CloudWatch để tập trung application log, giám sát CPU của EC2.

## Mục tiêu

- Đưa ứng dụng Spring MVC/Thymeleaf hiện có lên môi trường demo học tập mà không thiết kế lại kiến trúc.
- Chuyển relational data từ MySQL/Laragon local sang RDS MySQL private.
- Lưu avatar và ảnh bài viết mới trên S3 private, đồng thời giữ tương thích với ảnh local cũ.
- Dùng EC2 IAM Role và AWS SDK default credential chain thay cho AWS key dài hạn.
- Gửi thông báo best-effort cho quản trị viên sau khi user mới được lưu thành công.
- Tập trung Spring Boot file log và giám sát tình trạng CPU cao kéo dài trên EC2.
- Kiểm soát chi phí shared account bằng AWS Budgets và quy trình cleanup rõ ràng.

## Phạm vi

Phạm vi đã triển khai gồm AWS Budgets, Amazon EC2, Amazon RDS for MySQL, Amazon S3, AWS IAM Role, Amazon CloudWatch Logs, một CloudWatch CPU alarm và Amazon SES trong Sandbox. Public endpoint vẫn là HTTP trực tiếp trên EC2 port `8080`. Nginx, HTTPS, Route 53, CloudFront, AWS WAF, ALB, Auto Scaling, NAT Gateway, Multi-AZ, SNS notification, public S3 hosting và SES domain identity không thuộc deployment hiện tại.

## Vấn đề cần giải quyết

Trước khi triển khai AWS, TechBlog chạy tại `http://localhost:8080`, kết nối MySQL/Laragon tại `localhost:3306/techblog` và lưu upload trong `storage/avatars`, `storage/posts`. Cách triển khai local phù hợp cho phát triển nhưng có các hạn chế:

- Người chấm bên ngoài máy phát triển không thể truy cập website ổn định.
- Database phụ thuộc vào một MySQL local.
- File upload gắn với vòng đời và ổ đĩa của một application host.
- Nhóm cần một môi trường thống nhất để demo Reader, Writer và Admin.
- Email đăng ký, log tập trung và infrastructure alarm cần có luồng AWS được kiểm soát.
- Tài nguyên cloud dùng chung cần cảnh báo chi phí, least-privilege access và quy trình cleanup.

Giải pháp phải xử lý các hạn chế này mà không đổi TechBlog thành kiến trúc ứng dụng khác hoặc đưa thêm dịch vụ không cần thiết.

## Tổng quan giải pháp

TechBlog hỗ trợ ba cấp phân quyền:

- **USER / Reader:** đăng ký, đăng nhập, đọc bài, like, save, comment, reply, report comment, cập nhật profile, upload avatar và gửi yêu cầu nâng cấp Writer.
- **EDITOR / Writer:** giữ quyền Reader và có thể tạo, sửa hoặc xóa bài của mình. Bài viết đi qua trạng thái `PENDING`, `APPROVED` hoặc `REJECTED`.
- **ADMIN:** quản lý user, category, comment, report, writer request và duyệt hoặc từ chối bài viết.

Giải pháp AWS giữ toàn bộ luồng trên trong một ứng dụng Spring Boot MVC. Amazon EC2 `techblog-ec2` chạy `target/techblog-0.0.1-SNAPSHOT.jar` dưới dạng `techblog.service`. Amazon RDS for MySQL `techblog-db` thay MySQL local. Amazon S3 bucket `techblog-uploads-ttv-2026` lưu object mới trong `avatars/`, `posts/`; MySQL lưu marker như `s3:avatars/<uuid>.<ext>` hoặc `s3:posts/<uuid>.<ext>`, không lưu binary ảnh. Ảnh local cũ vẫn được hỗ trợ. Sau khi user record được lưu thành công, Amazon SES gửi best-effort notification đến admin recipient đã verify và được cấu hình. CloudWatch Agent gửi `/var/log/techblog/application.log` đến `/techblog/application`, còn `techblog-high-cpu` theo dõi CPU EC2 cao kéo dài.

S3 bucket luôn private, bật Block Public Access và SSE-S3. Spring Boot truy cập S3 và SES bằng AWS SDK for Java cùng EC2 IAM Role, không dùng static key. Media resolver đã triển khai tạo presigned GET URL có thời hạn 10 phút khi render reference bắt đầu bằng `s3:`; presigned URL không được lưu trong MySQL.

## Kiến trúc giải pháp

Sơ đồ tách luồng request chính, integration của ứng dụng, monitoring và cost control. S3, CloudWatch, SES, IAM và Budgets là dịch vụ AWS nằm ngoài application subnet.

{{< mermaid align="center" >}}
flowchart LR
    U[Reader / Writer / Admin<br/>Trình duyệt] -->|HTTP :8080| G[Internet Gateway]
    subgraph VPC[Amazon VPC]
        subgraph PUB[Public Subnet]
            E[Amazon EC2<br/>techblog-ec2<br/>Spring Boot JAR + systemd]
        end
        subgraph DATA[Đường database private]
            R[(Amazon RDS for MySQL<br/>techblog-db<br/>Database: techblog)]
        end
        G --> E
        E -->|JDBC 3306| R
    end
    E -. IAM instance profile .-> I[AWS IAM Role<br/>techblog-ec2-role]
    E -->|AWS SDK<br/>upload / đọc / xóa| S[(Amazon S3<br/>techblog-uploads-ttv-2026)]
    E -->|AWS SDK<br/>thông báo đăng ký cho admin| M[Amazon SES<br/>Sandbox]
    E -->|CloudWatch Agent<br/>application logs| C[Amazon CloudWatch Logs<br/>/techblog/application]
    E -->|CPUUtilization metric| A[CloudWatch Alarm<br/>techblog-high-cpu]
    B[AWS Budgets] --> O[Cảnh báo chi phí AWS account]
{{< /mermaid >}}

Sơ đồ dưới đây trình bày workflow TechBlog đã triển khai, từ public request đến database, object storage, transactional email và monitoring integration.

<figure>
  {{< content-image src="images/2-Proposal/Techblog_Workflow_AWS.png" alt="Workflow TechBlog đã triển khai trên AWS" >}}
  <figcaption><em>Hình 2.1. Workflow TechBlog đã triển khai trên AWS tại ap-southeast-1.</em></figcaption>
</figure>

Quy tắc kiến trúc của bản demo:

- Trình duyệt truy cập thẳng EC2 qua HTTP port `8080`; luồng hiện tại không có Nginx, HTTPS, domain, load balancer, CloudFront hoặc WAF.
- RDS không public. Security Group chỉ nhận TCP `3306` từ EC2 Security Group.
- EC2 dùng `techblog-ec2-role` để lấy temporary credential cho S3, SES và CloudWatch.
- S3 không dùng làm static website và không đặt bên trong VPC hoặc subnet.
- AWS Budgets là cơ chế kiểm soát billing, không nằm trong runtime request.

## Dịch vụ AWS sử dụng

| Dịch vụ | Vai trò triển khai | Lý do lựa chọn |
|---|---|---|
| AWS Budgets | Đã cấu hình cho shared account | Cảnh báo chi phí sớm; Budget không tự động dừng tài nguyên. |
| Amazon EC2 | Chạy Spring Boot executable JAR | Phù hợp ứng dụng monolithic MVC và giúp nhóm kiểm soát runtime trực tiếp. |
| Amazon RDS for MySQL | Lưu private database `techblog` | Thay MySQL local và giới hạn database access chỉ từ EC2. |
| Amazon S3 | Lưu object avatar và ảnh bài viết | Tách object storage bền vững khỏi ổ đĩa EC2 trong khi bucket vẫn private. |
| AWS IAM Role | Cấp temporary credential cho EC2 | Hiện dùng `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` và inline `TechBlogSesSendEmail`; không lưu static AWS key. |
| Amazon CloudWatch Logs | Tập trung application logs | Thu event phù hợp từ `/var/log/techblog/application.log` vào Standard-class Log Group `/techblog/application` với retention 14 ngày. |
| Amazon CloudWatch Alarm | Giám sát CPU cao kéo dài | `techblog-high-cpu` đánh giá Average `CPUUtilization > 70%` trong 2/2 period, mỗi period 5 phút; không có notification action. |
| Amazon SES | Gửi thông báo vận hành khi có đăng ký | Dùng verified email identity và gửi đến admin recipient đã verify trong khi account còn ở Sandbox. |

`systemd` không phải AWS Service; đây là Linux process manager dùng trên EC2 cho `techblog.service`.

## Kế hoạch triển khai kỹ thuật

1. Kiểm tra Java 17, Maven Wrapper, MySQL/Laragon, local build và toàn bộ luồng Reader/Writer/Admin.
2. Dùng shared AWS account an toàn, chọn `ap-southeast-1` và tạo AWS Budgets trước các tài nguyên có tính phí.
3. Tạo S3 bucket private `techblog-uploads-ttv-2026` với Block Public Access, SSE-S3 và prefix `avatars/`, `posts/`.
4. Tạo `techblog-ec2-role` và chuẩn bị policy cho S3, CloudWatch, SES. Chưa gắn role vào instance hoặc chạy runtime test trên EC2 khi EC2 chưa tồn tại.
5. Tạo và xác minh Amazon SES email identity, hoàn thiện policy `TechBlogSesSendEmail` giới hạn theo identity, sau đó chỉ review Budget, S3, IAM và SES trên console.
6. Tạo RDS MySQL Single-AZ private `techblog-db`; chuẩn bị EC2 Security Group và chỉ cho group này truy cập RDS port `3306`.
7. Ghi RDS endpoint bằng `<RDS_ENDPOINT>` và export dữ liệu local thành `techblog.sql`. Chưa chạy lệnh kết nối hoặc import trên EC2.
8. Launch `techblog-ec2` bằng Amazon Linux 2023, `t3.micro`, 8 GiB gp3, Security Group đã chuẩn bị và IAM instance profile `techblog-ec2-role`.
9. Cài Java 17 Amazon Corretto và MariaDB client. Kết nối từ EC2 đến RDS, chuyển `techblog.sql`, import và kiểm tra chín bảng TechBlog.
10. Build `target/techblog-0.0.1-SNAPSHOT.jar` bằng Maven Wrapper và upload lên EC2.
11. Cấu hình RDS, S3 provider/bucket/presign lifetime, SES sender/admin recipient, log file và Region trong `/etc/techblog/techblog.env`; giới hạn file bằng mode `600`.
12. Cấu hình `/etc/systemd/system/techblog.service`, start ứng dụng và kiểm tra HTTP 200 trên port `8080`.
13. Cài và cấu hình CloudWatch Agent cho `/var/log/techblog/application.log`, Log Group `/techblog/application` và retention 14 ngày.
14. Tạo `techblog-high-cpu` với Average `CPUUtilization > 70%`, period 5 phút, evaluation 2/2 và missing data là not breaching. Theo dõi trên console vì chưa có SNS action.
15. Kiểm thử chức năng, S3 create/read/replace/delete, SES console/CLI send và admin registration notification, CloudWatch log event, CPU alarm definition cùng luồng regression HTTP 413. Lưu bằng chứng đã che thông tin nhạy cảm và thực hiện cleanup checklist.

## Phạm vi nhóm

TechBlog được phát triển và triển khai như project kỹ thuật dùng chung của nhóm TTV. Các phần **2-Proposal**, **3-BlogsPosted** và **5-Workshop** là deliverable thống nhất, phải mô tả cùng một ứng dụng và môi trường AWS.

Trang đầu, **1-Worklog**, **4-EventParticipated**, **6-Self-evaluation** và **7-Feedback** vẫn là nội dung cá nhân của từng sinh viên. Nhóm dùng một môi trường AWS chung để kiểm soát chi phí và tránh tạo trùng tài nguyên. Nhóm không chia sẻ root credentials; root chỉ dùng cho billing hoặc account recovery. Thành viên cần truy cập trực tiếp sẽ được cấp IAM user hoặc IAM role riêng theo least privilege.

## Phân công nhóm

| Thành viên | Trách nhiệm chính | Phạm vi cân bằng |
|---|---|---|
| Thành viên 1 | AWS Budgets, IAM Role, S3 infrastructure và kiểm thử S3 | Cost guardrail, bucket security, review policy hiện tại, hướng thu hẹp quyền S3 và bằng chứng upload/read/delete. |
| Thành viên 2 | RDS MySQL, Security Groups, migration và kiểm tra dữ liệu | Private database, EC2-to-RDS rule, export/import, schema check và xác minh object reference. |
| Thành viên 3 | EC2, Java, runtime configuration, systemd và SES | Instance setup, JAR deployment, environment variable, persistent service, verified identity, CLI test và admin-notification test. |
| Thành viên 4 | Functional test, CloudWatch Logs, alarm, security và troubleshooting | Role matrix, centralized log collection, CPU alarm, HTTP 413 regression, operational check và security review. |

Cả bốn thành viên cùng duyệt 5.1, 5.2, 5.7 và Proposal. Nhóm không cần bốn AWS account và không dùng chung root account. AWS Budgets kiểm soát toàn bộ shared environment, còn EC2 IAM Role quan trọng hơn static credential trong ứng dụng.

## Lộ trình và mốc triển khai

| Thời gian | Hoạt động | Mốc kết quả |
|---|---|---|
| Tuần 1 | Kiểm tra ứng dụng local, account access, Region và Budgets | Có local baseline và cost guardrail |
| Tuần 2 | Tạo S3, IAM Role, SES identity, RDS và Security Groups | Có nền tảng storage, email, identity và database |
| Tuần 3 | Launch EC2, cài runtime, migrate dữ liệu và upload JAR | TechBlog chạy trên EC2 và kết nối RDS |
| Tuần 4 | Cấu hình S3/SES runtime, CloudWatch Agent, systemd và CPU alarm | Có application integration và operational visibility |
| Tuần 5 | Kiểm thử chức năng, upload, admin email, log, alarm, security và cleanup | Hoàn tất workshop có bằng chứng |

## Bảo mật

- EC2 nhận temporary credential từ `techblog-ec2-role`; source code và environment file không chứa Access Key hoặc Secret Access Key.
- S3 bucket private, bật Block Public Access, tắt ACL và dùng SSE-S3. Presigned GET URL có thời hạn 10 phút.
- Spring nhận multipart file và request tối đa 50 MB ở lớp vận chuyển. Application validation giới hạn riêng avatar 5 MiB, ảnh bài viết 15 MiB; chỉ chấp nhận signature JPEG, PNG, GIF hoặc WebP và từ chối SVG/file giả dạng ảnh.
- RDS không public và chỉ nhận port `3306` từ EC2 Security Group.
- SES dùng verified email identity. Inline policy chỉ cho `ses:SendEmail` và `ses:SendRawEmail` đối với identity đã xác minh.
- `AmazonS3FullAccess` rộng hơn nhu cầu ứng dụng. Thay bằng permission giới hạn bucket và prefix là cải tiến bảo mật tương lai.
- `/etc/techblog/techblog.env` có mode `600`; screenshot và report chỉ dùng placeholder.

## Giám sát

CloudWatch Agent đọc `/var/log/techblog/application.log` và gửi đến Standard-class Log Group `/techblog/application`. Log stream dùng EC2 instance ID, retention là 14 ngày. Log không được chứa password, hash, JWT, cookie, credential hoặc dữ liệu người dùng nhạy cảm đầy đủ.

Alarm đã triển khai chỉ có `techblog-high-cpu`: Average `CPUUtilization` lớn hơn 70%, period 5 phút, 2 trong 2 datapoint và missing data được xem là good/not breaching. Alarm không có SNS notification action nên nhóm theo dõi trên CloudWatch console.

## Ước tính ngân sách và tối ưu chi phí

EC2 và RDS là hai nguồn chi phí chính. S3, CloudWatch và SES chỉ tạo chi phí nhỏ ở quy mô demo nhưng vẫn phải theo dõi. Nhóm áp dụng các biện pháp:

- Giữ EC2 ở `t3.micro`, 8 GiB gp3 và RDS ở `db.t3.micro`, Single-AZ.
- Không tạo NAT Gateway, ALB, Auto Scaling, Multi-AZ RDS hoặc tài nguyên không cần cho demo đầu.
- Đặt CloudWatch Logs retention 14 ngày, không thu dữ liệu debug ít giá trị hoặc thông tin nhạy cảm.
- Tắt S3 versioning và Transfer Acceleration trong demo; xóa test object không còn cần.
- Dùng Amazon SES tiêu chuẩn, không dùng Dedicated IP, Mail Manager, Global Endpoint hoặc attachment.
- Stop hoặc xóa tài nguyên sau khi lấy bằng chứng và thường xuyên kiểm tra AWS Budgets.

Ảnh chụp Billing quan sát ngày **2026-07-13**:

| Hạng mục Billing | Giá trị quan sát |
|---|---:|
| Credit ban đầu | 200,00 USD |
| Chi phí month-to-date | 2,57 USD |
| Credit ước tính đã dùng | 2,74 USD |
| Credit ước tính còn lại | 197,26 USD |
| Forecast tháng hiện tại | 6,89 USD |

Forecast là dự báo cho cả tháng, không phải số tiền đã bị trừ.

## Đánh giá rủi ro

| Rủi ro | Ảnh hưởng | Cách giảm thiểu |
|---|---|---|
| EC2 public IP đổi sau stop/start | URL demo không còn đúng | Kiểm tra IP hiện tại và giữ `<EC2_PUBLIC_IP>` trong report. |
| HTTP port `8080` public | Traffic không mã hóa | Chỉ mở trong thời gian demo, tránh dữ liệu nhạy cảm và đưa HTTPS vào hướng cải tiến. |
| Sai RDS endpoint, password hoặc SG | Ứng dụng không start hoặc query được | Test từ EC2, dùng placeholder trong tài liệu và giới hạn `3306` theo EC2 SG. |
| Sai S3 permission hoặc object reference | Ảnh không hiển thị hoặc còn object mồ côi | Test create/read/replace/delete và kiểm tra database lưu đúng object key. |
| Giới hạn SES Sandbox | Chỉ gửi được admin notification đến recipient đã verify | Giữ sender và admin recipient ở trạng thái Verified; không tuyên bố gửi đến mọi email đăng ký. |
| CloudWatch log chứa dữ liệu nhạy cảm | Lộ secret hoặc dữ liệu cá nhân | Chỉ thu INFO/WARN/ERROR và loại password, token, cookie, nội dung cá nhân. |
| Alarm không có notification integration | Nhóm có thể bỏ lỡ console-only alarm | Theo dõi CloudWatch console trong demo; chỉ thêm SNS sau khi triển khai thật. |
| Chi phí ngoài dự kiến | Shared credit bị tiêu hao | Dùng Budgets, retention ngắn, instance nhỏ và cleanup đầy đủ. |

## Kết quả kỳ vọng

- `techblog-ec2` chạy Spring Boot JAR bằng `techblog.service` và trả HTTP 200 trên port `8080`.
- `techblog-db` lưu private schema và dữ liệu TechBlog đã migrate.
- Luồng Reader, Writer và Admin hoạt động với AWS database.
- Avatar và ảnh bài viết được tạo, đọc, thay thế và xóa đúng prefix S3 private thông qua ứng dụng.
- Đăng ký user không bị rollback khi SES tạm lỗi; khi SES thành công, admin recipient đã verify nhận notification đăng ký không chứa dữ liệu nhạy cảm.
- CloudWatch nhận application log không chứa dữ liệu nhạy cảm và hiển thị alarm `techblog-high-cpu` không có notification action.
- Nhóm giải thích được security boundary đã triển khai, giới hạn thật, cost control và bằng chứng troubleshooting.

## Giới hạn hiện tại

Demo public bằng HTTP và EC2 public IP có thể thay đổi trên port `8080`. Hệ thống dùng một EC2 nhỏ và RDS Single-AZ, lưu database credential trong environment file được bảo vệ, vẫn ở SES Sandbox và phải xem `techblog-high-cpu` trên console vì chưa có SNS action. Managed policy S3 hiện tại được ghi đúng là policy rộng, không mô tả sai thành least privilege.

## Hướng phát triển tương lai

Hướng phát triển có thể thay `AmazonS3FullAccess` bằng custom policy giới hạn bucket/prefix; bổ sung Nginx, HTTPS, domain với Route 53, CloudFront, AWS WAF, Application Load Balancer, Auto Scaling, Multi-AZ RDS, AWS Secrets Manager hoặc AWS Systems Manager Parameter Store, Flyway/Liquibase, quy trình kiểm thử backup/restore và SNS action cho alarm. Không trình bày các thành phần này như kiến trúc runtime hiện tại cho đến khi nhóm triển khai và xác minh thật.
