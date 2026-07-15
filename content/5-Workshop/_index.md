---
title: "TechBlog Workshop on AWS"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

{{% notice info %}}
### Access the live demo



[http://13.212.52.148:8080](http://13.212.52.148:8080)


{{% /notice %}}

## Workshop Overview

This hands-on workshop documents how group TTV moves TechBlog from IntelliJ, Laragon, local MySQL, and local upload folders to one AWS demo environment in `ap-southeast-1`. TechBlog keeps its Java 17 and Spring Boot 3.5.12 monolithic MVC architecture: Thymeleaf renders the user interface, Spring Security controls USER/EDITOR/ADMIN access, Spring Data JPA/Hibernate communicates with MySQL, and Maven Wrapper produces one executable JAR.

The workshop follows the implementation as one continuous deployment process. It establishes cost controls, S3 object storage, an EC2 IAM Role, Amazon SES identity, private RDS MySQL, EC2 runtime configuration, CloudWatch Logs, one CloudWatch CPU alarm, functional verification, and cleanup. The application remains directly accessible for the demo at:

```text
http://<EC2_PUBLIC_IP>:8080/
```

No step requires a static AWS Access Key or Secret Access Key in source code.

## Architecture Summary

The main request path is `User → Internet Gateway → EC2 Spring Boot → RDS MySQL`. The same EC2 instance uses `techblog-ec2-role` to call S3 for avatar/post images, Amazon SES for the administrator notification, and CloudWatch for centralized logs and the EC2 CPU alarm.

- `techblog-ec2`: Amazon Linux 2023, Java 17 Amazon Corretto, `t3.micro`, 8 GiB gp3.
- `techblog-db`: private Amazon RDS for MySQL, `db.t3.micro`, Single-AZ, port `3306`.
- `techblog-uploads-ttv-2026`: private S3 bucket with `avatars/` and `posts/`.
- `techblog.service`: `systemd` unit that runs the Spring Boot JAR.
- `/techblog/application`: Standard-class CloudWatch Log Group with 14-day retention and a stream named by EC2 instance ID.
- `techblog-high-cpu`: Average CPUUtilization alarm at greater than 70% for 2 of 2 five-minute periods, with no notification action.
- Amazon SES: verified email identity in Sandbox for an administrator notification after successful registration.

HTTPS, Nginx, Route 53, CloudFront, AWS WAF, ALB, Auto Scaling, Multi-AZ, API Gateway, Amplify, Elastic Beanstalk, and NAT Gateway are outside the current demo architecture.

## AWS Services Used

| Service | Workshop purpose |
|---|---|
| AWS Budgets | Warns the group about shared-account spending. |
| Amazon EC2 | Runs the Spring Boot MVC JAR. |
| Amazon RDS for MySQL | Hosts the private `techblog` database. |
| Amazon S3 | Stores avatar and post-image objects in a private bucket. |
| AWS IAM | Gives EC2 temporary credentials through the current `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy`, and `TechBlogSesSendEmail` policies. |
| Amazon SES | Sends a best-effort registration notice to the configured verified administrator recipient while SES remains in Sandbox. |
| Amazon CloudWatch Logs | Centralizes non-sensitive Spring Boot logs. |
| Amazon CloudWatch Alarm | Tracks sustained EC2 CPU usage; no SNS notification action is configured. |

## Workshop Modules

1. [Workshop Overview](5.1-workshop-overview/)
2. [Prerequisites](5.2-prerequisites/)
3. [Foundation Services](5.3-budget-s3-iam/)
   - [Create AWS Budget](5.3-budget-s3-iam/5.3.1-budget/)
   - [Create and Secure the S3 Bucket](5.3-budget-s3-iam/5.3.2-s3/)
   - [Create and Prepare the EC2 IAM Role](5.3-budget-s3-iam/5.3.3-iam-role/)
   - [Configure an Amazon SES Verified Identity](5.3-budget-s3-iam/5.3.4-ses/)
   - [Verify Budget, S3, IAM, and SES](5.3-budget-s3-iam/5.3.5-verify/)
4. [Prepare RDS MySQL](5.4-rds-mysql/)
   - Create private RDS, prepare the EC2-to-RDS security-group boundary, record connection values, and export the local database.
5. [Deploy Spring Boot to EC2](5.5-deploy-ec2/)
   - Create EC2, attach the IAM Role, install Java, connect/import RDS, upload the JAR, configure RDS/S3/SES runtime values, and run `systemd`.
6. [Testing, Monitoring, and Operations](5.6-testing-operations/)
   - Test roles, S3 upload and HTTP 413, SES console/CLI/application flows, local logs, CloudWatch Logs, the CPU alarm, security, and troubleshooting.
7. [Cleanup](5.7-cleanup/)

## Expected Result

At the end of the workshop, TechBlog runs as one Spring Boot service on EC2, uses private RDS MySQL for relational data, stores new avatar and post images in private S3 with 10-minute presigned GET URLs, sends a best-effort administrator registration notice through SES, and exposes operational evidence in CloudWatch Logs and `techblog-high-cpu`. Missing screenshots are tracked only in hidden comments until the group provides real, redacted evidence.
