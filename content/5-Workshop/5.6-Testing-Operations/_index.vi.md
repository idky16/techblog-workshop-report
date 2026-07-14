---
title: "Kiểm thử, monitoring và vận hành"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
alwaysopen: true
---

## Mục tiêu module

Kiểm tra TechBlog vừa là ứng dụng vừa là AWS workload. Module tách role-based behavior, S3 object lifecycle, SES email, local process check, centralized log, EC2 alarm và security troubleshooting để mỗi kết quả có thể demo độc lập.

## Luồng module

{{< mermaid align="center" >}}
flowchart LR
    W[Public TechBlog<br/>:8080] --> F[Functional tests]
    F --> S[S3 upload lifecycle<br/>và HTTP 413]
    F --> M[SES admin registration notification]
    W --> L[systemd và local logs]
    L --> C[CloudWatch Logs]
    E[EC2 CPUUtilization] --> A[CloudWatch Alarm<br/>techblog-high-cpu]
    S --> R[Security review<br/>và troubleshooting]
    M --> R
    C --> R
    A --> R
{{< /mermaid >}}

## Trang con

1. [Kiểm thử chức năng và phân quyền](5.6.1-functional-testing/)
2. [Kiểm thử S3 upload và xử lý HTTP 413](5.6.2-upload-413/)
3. [Kiểm thử transactional email bằng Amazon SES](5.6.3-ses-testing/)
4. [Kiểm tra systemd và application log local](5.6.4-service-logs/)
5. [Cấu hình và kiểm tra CloudWatch Logs](5.6.5-cloudwatch-logs/)
6. [Tạo và kiểm tra CloudWatch CPU Alarm](5.6.6-cloudwatch-alarms/)
7. [Review bảo mật và troubleshooting](5.6.7-security-troubleshooting/)

## Kết quả module

| Phạm vi | Bằng chứng mong đợi |
|---|---|
| Functional role | Ma trận test Guest, USER, EDITOR và ADMIN |
| S3 | Object avatar/post và database reference tương ứng |
| Kiểm tra upload | Test regression avatar 2,09 MB pass; transport limit 50 MB, avatar limit 5 MiB, post-image limit 15 MiB và có signature validation |
| SES | Console và IAM Role CLI send pass; admin đã verify nhận application notification; registration không fail khi mail lỗi |
| Local operations | Service active, Java process, port 8080 và local log an toàn |
| CloudWatch Logs | Event mới trong `/techblog/application` |
| CloudWatch Alarm | Definition `techblog-high-cpu` không có notification action |
| Security | Review SG, IAM, S3, SES, secret và public-port boundary |

## Quy tắc bằng chứng

Không tự tạo object key, message ID, email, log-stream name, alarm state, ARN hoặc screenshot. Bằng chứng còn thiếu chỉ được theo dõi bằng comment Markdown ẩn cho đến khi có ảnh thật đã che thông tin.
