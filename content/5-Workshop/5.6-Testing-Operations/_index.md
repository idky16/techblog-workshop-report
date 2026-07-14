---
title: "Testing, Monitoring, and Operations"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
alwaysopen: true
---

## Module Objective

Verify TechBlog as an application and as an AWS workload. The module separates role-based behavior, S3 object lifecycle, SES email, local process checks, centralized logs, the EC2 CPU alarm, and security troubleshooting so each result can be demonstrated independently.

## Module Flow

{{< mermaid align="center" >}}
flowchart LR
    W[Public TechBlog<br/>:8080] --> F[Functional tests]
    F --> S[S3 upload lifecycle<br/>and HTTP 413]
    F --> M[SES admin registration notice]
    W --> L[systemd and local logs]
    L --> C[CloudWatch Logs]
    E[EC2 CPUUtilization] --> A[CloudWatch Alarm<br/>techblog-high-cpu]
    S --> R[Security review<br/>and troubleshooting]
    M --> R
    C --> R
    A --> R
{{< /mermaid >}}

## Child Pages

1. [Functional and Role-Based Testing](5.6.1-functional-testing/)
2. [Test S3 Upload and Troubleshoot HTTP 413](5.6.2-upload-413/)
3. [Test Amazon SES Transactional Email](5.6.3-ses-testing/)
4. [Check systemd and Local Application Logs](5.6.4-service-logs/)
5. [Configure and Verify CloudWatch Logs](5.6.5-cloudwatch-logs/)
6. [Create and Verify the CloudWatch CPU Alarm](5.6.6-cloudwatch-alarms/)
7. [Security Review and Troubleshooting](5.6.7-security-troubleshooting/)

## Module Result

| Area | Expected evidence |
|---|---|
| Functional roles | Guest, USER, EDITOR, and ADMIN test matrix |
| S3 | Avatar/post objects and matching database references |
| Upload validation | 2.09 MB avatar regression passes; transport limit 50 MB, avatar limit 5 MiB, post-image limit 15 MiB, signature validation enabled |
| SES | Console and IAM Role CLI sends pass; verified administrator receives the application notice; registration survives mail failure |
| Local operations | Active service, Java process, port 8080, and safe local logs |
| CloudWatch Logs | New event in `/techblog/application` |
| CloudWatch Alarm | `techblog-high-cpu` definition with no notification action |
| Security | Reviewed SG, IAM, S3, SES, secret, and public-port boundaries |

## Evidence Rule

Do not invent object keys, message IDs, email addresses, log-stream names, alarm states, ARNs, or screenshots. Missing evidence is tracked only in hidden Markdown comments until a real redacted screenshot is available.
