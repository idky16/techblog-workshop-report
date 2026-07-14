---
title: "TechBlog Deployment Proposal on AWS"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## Executive Summary

TechBlog is the shared technical project of group TTV. It is a technology blogging web application built with Java 17, Spring Boot 3.5.12, Spring MVC, Spring Security, Spring Data JPA/Hibernate, Thymeleaf, MySQL, and Maven Wrapper. The frontend and backend are packaged in the same Spring Boot executable JAR; TechBlog remains a server-rendered monolithic MVC application rather than a React SPA or a separate API platform.

This proposal moves the application from local IntelliJ/Laragon execution to one cost-conscious AWS demo environment in Asia Pacific (Singapore), `ap-southeast-1`. Users access the application directly through HTTP port `8080` on Amazon EC2. The application uses a private Amazon RDS for MySQL database, stores avatar and post-image objects in a private Amazon S3 bucket, sends transactional email through Amazon SES, and publishes application logs and operational metrics to Amazon CloudWatch. AWS IAM Role supplies temporary credentials to EC2, AWS Budgets controls team spending, and `systemd` keeps the Spring Boot process running.

The demo URL uses a placeholder because the EC2 public IP can change:

```text
http://<EC2_PUBLIC_IP>:8080/
```

## Introduction

The proposal records the final, verified AWS implementation rather than an aspirational architecture. TechBlog runs as `techblog.service` on Amazon Linux 2023 with `WorkingDirectory=/home/ec2-user`. It uses private Amazon RDS for MySQL for business data, private Amazon S3 for new media, Amazon SES for an administrator registration notification, and Amazon CloudWatch for centralized application logs and EC2 CPU monitoring.

## Objectives

- Make the existing Spring MVC/Thymeleaf application reachable for a controlled learning demo without redesigning it.
- Move persistent relational data from local MySQL/Laragon to private RDS MySQL.
- Store new avatar and post-image objects in private S3 while preserving legacy local-image compatibility.
- Use the EC2 IAM Role and AWS SDK default credential chain instead of long-lived AWS keys.
- Send a best-effort administrator notification after a new user is saved successfully.
- Centralize the Spring Boot file log and monitor sustained EC2 CPU usage.
- Control shared-account spending with AWS Budgets and an explicit cleanup procedure.

## Scope

The implemented scope includes AWS Budgets, Amazon EC2, Amazon RDS for MySQL, Amazon S3, AWS IAM Role, Amazon CloudWatch Logs, one CloudWatch CPU alarm, and Amazon SES in Sandbox. The public endpoint remains direct HTTP on EC2 port `8080`. Nginx, HTTPS, Route 53, CloudFront, AWS WAF, ALB, Auto Scaling, NAT Gateway, Multi-AZ, SNS notification, public S3 hosting, and SES domain identity are outside the current deployment.

## Problem Statement

Before AWS deployment, TechBlog ran at `http://localhost:8080`, connected to MySQL/Laragon at `localhost:3306/techblog`, and stored uploads under `storage/avatars` and `storage/posts`. This local arrangement supports development but creates several limitations:

- Reviewers outside the development machine cannot reliably access the website.
- Database availability depends on one local MySQL installation.
- Local uploads are tied to the lifecycle and disk of one application host.
- The group needs one consistent environment for Reader, Writer, and Admin demonstrations.
- Registration email, centralized log review, and infrastructure alarms need a controlled AWS path.
- Shared cloud resources require cost alerts, least-privilege access, and a cleanup procedure.

The solution must address these limitations without changing TechBlog into a different application architecture or introducing unnecessary services.

## Solution Overview

TechBlog supports three authorization levels:

- **USER / Reader:** signs up, signs in, reads posts, likes, saves, comments, replies, reports comments, updates a profile, uploads an avatar, and requests Writer access.
- **EDITOR / Writer:** retains Reader permissions and creates, edits, or deletes owned posts. Each post follows `PENDING`, `APPROVED`, or `REJECTED` moderation status.
- **ADMIN:** manages users, categories, comments, reports, Writer requests, and post approval or rejection.

The AWS solution keeps these flows inside one Spring Boot MVC application. Amazon EC2 `techblog-ec2` runs `target/techblog-0.0.1-SNAPSHOT.jar` as `techblog.service`. Amazon RDS for MySQL `techblog-db` replaces local MySQL. Amazon S3 bucket `techblog-uploads-ttv-2026` stores new objects under `avatars/` and `posts/`; MySQL stores markers such as `s3:avatars/<uuid>.<ext>` or `s3:posts/<uuid>.<ext>` rather than image binary data. Legacy local images remain supported. After a user record is saved successfully, Amazon SES sends a best-effort notification to the configured verified administrator recipient. CloudWatch Agent sends `/var/log/techblog/application.log` to `/techblog/application`, and `techblog-high-cpu` monitors sustained EC2 CPU usage.

The S3 bucket remains private with Block Public Access and SSE-S3. Spring Boot accesses S3 and SES through AWS SDK for Java and the EC2 IAM Role, not through static keys. The implemented media resolver creates presigned GET URLs with a 10-minute lifetime when rendering an `s3:` reference; presigned URLs are never stored in MySQL.

## Solution Architecture

The diagram separates the active request path, application integrations, monitoring, and cost control. S3, CloudWatch, SES, IAM, and Budgets are AWS services outside the application subnet.

{{< mermaid align="center" >}}
flowchart LR
    U[Reader / Writer / Admin<br/>Web Browser] -->|HTTP :8080| G[Internet Gateway]
    subgraph VPC[Amazon VPC]
        subgraph PUB[Public Subnet]
            E[Amazon EC2<br/>techblog-ec2<br/>Spring Boot JAR + systemd]
        end
        subgraph DATA[Private database path]
            R[(Amazon RDS for MySQL<br/>techblog-db<br/>Database: techblog)]
        end
        G --> E
        E -->|JDBC 3306| R
    end
    E -. IAM instance profile .-> I[AWS IAM Role<br/>techblog-ec2-role]
    E -->|AWS SDK<br/>upload / read / delete| S[(Amazon S3<br/>techblog-uploads-ttv-2026)]
    E -->|AWS SDK<br/>admin registration notice| M[Amazon SES<br/>Sandbox]
    E -->|CloudWatch Agent<br/>application logs| C[Amazon CloudWatch Logs<br/>/techblog/application]
    E -->|CPUUtilization metric| A[CloudWatch Alarm<br/>techblog-high-cpu]
    B[AWS Budgets] --> O[AWS account cost alerts]
{{< /mermaid >}}

The following diagram presents the implemented TechBlog workflow from the public request path to the database, object storage, transactional email, and monitoring integrations.

<figure>
  {{< content-image src="images/2-Proposal/Techblog_Workflow_AWS.png" alt="Implemented TechBlog workflow on AWS" >}}
  <figcaption><em>Figure 2.1. Implemented TechBlog workflow on AWS in ap-southeast-1.</em></figcaption>
</figure>

Architecture rules for the demo:

- The browser reaches EC2 directly through HTTP port `8080`; there is no Nginx, HTTPS, domain, load balancer, CloudFront, or WAF in the active path.
- RDS is not publicly accessible. Its security group accepts TCP `3306` only from the EC2 security group.
- EC2 uses `techblog-ec2-role` for temporary credentials to S3, SES, and CloudWatch.
- S3 is not a static website and is not placed inside a VPC or subnet.
- AWS Budgets is a billing control and does not participate in runtime requests.

## AWS Services Used

| Service | Deployment role | Why it is selected |
|---|---|---|
| AWS Budgets | Configured for the shared account | Provides early cost notifications; it does not automatically stop resources. |
| Amazon EC2 | Runs the Spring Boot executable JAR | Fits the monolithic MVC application and gives the group direct runtime control. |
| Amazon RDS for MySQL | Hosts private database `techblog` | Replaces local MySQL and limits database access to EC2. |
| Amazon S3 | Stores avatar and post-image objects | Separates durable object storage from the EC2 filesystem while keeping the bucket private. |
| AWS IAM Role | Supplies temporary credentials to EC2 | Uses current `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy`, and inline `TechBlogSesSendEmail`; no static AWS key is stored. |
| Amazon CloudWatch Logs | Centralizes application logs | Collects suitable events from `/var/log/techblog/application.log` into Standard-class Log Group `/techblog/application` with 14-day retention. |
| Amazon CloudWatch Alarm | Monitors sustained EC2 CPU | `techblog-high-cpu` evaluates Average `CPUUtilization > 70%` for 2 of 2 five-minute periods; it has no notification action. |
| Amazon SES | Sends an operational registration notice | Uses a verified email identity and sends to the configured verified administrator recipient while the account remains in Sandbox. |

`systemd` is not an AWS service; it is the Linux process manager used on EC2 for `techblog.service`.

## Technical Implementation Plan

1. Verify Java 17, Maven Wrapper, MySQL/Laragon, the local build, and all Reader/Writer/Admin flows.
2. Use the shared AWS account safely, select `ap-southeast-1`, and create AWS Budgets before chargeable resources.
3. Create private S3 bucket `techblog-uploads-ttv-2026` with Block Public Access, SSE-S3, and prefixes `avatars/` and `posts/`.
4. Create `techblog-ec2-role` and prepare its S3, CloudWatch, and SES policies. Do not attach the role to an instance or perform an EC2 runtime test before EC2 exists.
5. Create and verify the Amazon SES email identity, complete the identity-scoped `TechBlogSesSendEmail` policy, and review Budget, S3, IAM, and SES in the console only.
6. Create private Single-AZ RDS MySQL `techblog-db`; prepare an EC2 security group and allow RDS port `3306` only from that group.
7. Record the RDS endpoint as `<RDS_ENDPOINT>` and export local data to `techblog.sql`. Do not run EC2 connection or import commands yet.
8. Launch `techblog-ec2` with Amazon Linux 2023, `t3.micro`, 8 GiB gp3, the prepared security group, and IAM instance profile `techblog-ec2-role`.
9. Install Java 17 Amazon Corretto and the MariaDB client. Connect from EC2 to RDS, transfer `techblog.sql`, import it, and verify the nine TechBlog tables.
10. Build `target/techblog-0.0.1-SNAPSHOT.jar` with Maven Wrapper and upload it to EC2.
11. Configure RDS, S3 provider/bucket/presign lifetime, SES sender/admin recipient, log-file, and Region values in `/etc/techblog/techblog.env`; restrict the file to mode `600`.
12. Configure `/etc/systemd/system/techblog.service`, start the application, and verify HTTP 200 on port `8080`.
13. Install and configure CloudWatch Agent for `/var/log/techblog/application.log`, Log Group `/techblog/application`, and 14-day retention.
14. Create `techblog-high-cpu` with Average `CPUUtilization > 70%`, five-minute period, 2-of-2 evaluation, and missing data treated as not breaching. Monitor it in the console because no SNS action is configured.
15. Test role-based functions, S3 create/read/replace/delete behavior, SES console/CLI sending and administrator registration notification, CloudWatch log events, the CPU alarm definition, and the HTTP 413 regression path. Capture redacted evidence and follow the cleanup checklist.

## Team Scope

TechBlog is developed and deployed as the shared technical project of group TTV. Sections **2-Proposal**, **3-BlogsPosted**, and **5-Workshop** are group-consistent deliverables and must describe the same application and AWS environment.

The landing page, **1-Worklog**, **4-EventParticipated**, **6-Self-evaluation**, and **7-Feedback** remain individual sections for each student. The group uses one shared AWS deployment environment to control cost and avoid duplicated resources. Root credentials are never shared; root is reserved for billing or account recovery. Members who require direct access receive separate IAM users or roles with least-privilege permissions.

## Team Responsibilities

| Member | Main responsibility | Balanced scope |
|---|---|---|
| Member 1 | AWS Budgets, IAM Role, S3 infrastructure, and S3 verification | Cost guardrails, bucket security, current-role review, future S3 permission narrowing, and upload/read/delete evidence. |
| Member 2 | RDS MySQL, Security Groups, migration, and data verification | Private database, EC2-to-RDS rule, export/import, schema checks, and object-reference verification. |
| Member 3 | EC2, Java, runtime configuration, systemd, and SES | Instance setup, JAR deployment, environment variables, persistent service, verified identity, CLI test, and administrator-notification test. |
| Member 4 | Functional tests, CloudWatch Logs, alarm, security, and troubleshooting | Role matrix, centralized log collection, CPU alarm, HTTP 413 regression, operational checks, and security review. |

All four members review 5.1, 5.2, 5.7, and this Proposal. The team does not need four AWS accounts and does not share the root account. AWS Budgets covers the shared environment, while the EC2 IAM Role is preferred over static application credentials.

## Timeline and Milestones

| Period | Activities | Milestone |
|---|---|---|
| Week 1 | Verify local application, account access, Region, and Budgets | Local baseline and cost guardrail are ready |
| Week 2 | Create S3, IAM Role, SES identity, RDS, and security groups | Storage, email, identity, and database foundation are ready |
| Week 3 | Launch EC2, install runtime tools, migrate data, and upload JAR | TechBlog runs on EC2 with RDS connectivity |
| Week 4 | Configure S3/SES runtime values, CloudWatch Agent, systemd, and the CPU alarm | Application integrations and operational visibility are configured |
| Week 5 | Execute functional, upload, admin-email, log, alarm, security, and cleanup tests | Evidence-backed workshop demo is complete |

## Security

- EC2 obtains temporary credentials from `techblog-ec2-role`; no Access Key or Secret Access Key is embedded in source code or the environment file.
- The S3 bucket is private with Block Public Access, ACLs disabled, and SSE-S3. A presigned GET URL lasts 10 minutes.
- Spring accepts multipart files and requests up to 50 MB at the transport layer. Application validation separately limits avatars to 5 MiB and post images to 15 MiB, accepts only JPEG, PNG, GIF, or WebP signatures, and rejects SVG or disguised non-image files.
- RDS is not public and accepts port `3306` only from the EC2 security group.
- SES uses a verified email identity. The inline policy permits only `ses:SendEmail` and `ses:SendRawEmail` for the verified identity.
- `AmazonS3FullAccess` is broader than the application requires. Replacing it with bucket-and-prefix-scoped permissions is a future security improvement.
- `/etc/techblog/techblog.env` is protected with mode `600`; screenshots and report text use placeholders.

## Monitoring

CloudWatch Agent reads `/var/log/techblog/application.log` and sends it to Standard-class Log Group `/techblog/application`. The stream name is the EC2 instance ID and retention is 14 days. Log statements must omit passwords, hashes, JWTs, cookies, credentials, and complete sensitive user data.

The deployed alarm is only `techblog-high-cpu`: Average `CPUUtilization` greater than 70%, a five-minute period, 2 of 2 datapoints, and missing data treated as good/not breaching. The alarm has no SNS notification action, so the team reviews it in the CloudWatch console.

## Budget Estimation and Cost Optimization

EC2 and RDS are the main cost drivers. S3, CloudWatch, and SES create smaller charges at demo volume, but they are still monitored. The team uses the following controls:

- Keep EC2 at `t3.micro`, 8 GiB gp3, and RDS at `db.t3.micro`, Single-AZ.
- Do not create NAT Gateway, ALB, Auto Scaling, Multi-AZ RDS, or other unnecessary resources for the first demo.
- Keep CloudWatch Logs retention at 14 days and avoid collecting sensitive or low-value debug data.
- Keep S3 versioning and Transfer Acceleration disabled for the demo; remove unnecessary test objects.
- Use standard Amazon SES sending without Dedicated IP, Mail Manager, Global Endpoint, or attachments.
- Stop or remove resources after evidence collection and review AWS Budgets regularly.

Billing snapshot observed on **2026-07-13**:

| Billing item | Observed value |
|---|---:|
| Initial credit | USD 200.00 |
| Month-to-date charges | USD 2.57 |
| Estimated credit used | USD 2.74 |
| Estimated credit remaining | USD 197.26 |
| Current-month forecast | USD 6.89 |

The forecast is an estimate for the month, not an amount already deducted.

## Risk Assessment

| Risk | Impact | Mitigation |
|---|---|---|
| EC2 public IP changes after stop/start | Demo URL becomes invalid | Re-check the current address and keep `<EC2_PUBLIC_IP>` in the report. |
| Public HTTP port `8080` | Traffic is unencrypted | Use only for the demo window, avoid sensitive data, and plan HTTPS as an improvement. |
| RDS endpoint, password, or SG error | Application cannot start or query data | Test from EC2, use placeholders in documentation, and restrict `3306` to the EC2 SG. |
| S3 access or object-reference error | Images fail to display or orphaned objects remain | Test create/read/replace/delete and verify the database stores the expected object key. |
| SES Sandbox restrictions | The administrator notification can be sent only to a verified recipient | Keep both sender and administrator recipient verified; do not claim delivery to arbitrary registration addresses. |
| CloudWatch log contains sensitive data | Secret or personal data exposure | Collect INFO/WARN/ERROR only and exclude passwords, tokens, cookies, and personal content. |
| Alarm is created without notification integration | Team may miss a console-only alarm | Review the CloudWatch console during the demo; add SNS only after it is actually deployed. |
| Unexpected cost | Shared credit is consumed | Use Budgets, short retention, small instances, and complete cleanup. |

## Expected Outcomes

- `techblog-ec2` runs the Spring Boot JAR through `techblog.service` and returns HTTP 200 on port `8080`.
- `techblog-db` privately stores the migrated TechBlog schema and application data.
- Reader, Writer, and Admin workflows operate against the AWS database.
- Avatar and post images are created, read, replaced, and deleted in the correct private S3 prefixes through the application.
- Registration succeeds independently of temporary SES errors, and the verified administrator recipient receives the non-sensitive registration notice when SES succeeds.
- CloudWatch receives non-sensitive application log events and displays the configured `techblog-high-cpu` alarm without a notification action.
- The group can explain the deployed security boundaries, actual limitations, cost controls, and evidence from troubleshooting.

## Current Limitations

The demo is exposed through HTTP and a changeable EC2 public IP on port `8080`. It uses one small EC2 instance and Single-AZ RDS, stores database credentials in a protected environment file, remains in the SES Sandbox, and requires console review of `techblog-high-cpu` because no SNS action exists. The current S3 managed policy is intentionally documented as broad rather than misrepresented as least privilege.

## Future Improvements

Future work may replace `AmazonS3FullAccess` with a bucket/prefix-scoped custom policy; add Nginx, HTTPS, a domain with Route 53, CloudFront, AWS WAF, an Application Load Balancer, Auto Scaling, Multi-AZ RDS, AWS Secrets Manager or AWS Systems Manager Parameter Store, Flyway/Liquibase migrations, tested backup/restore procedures, and an SNS alarm action. These items must not be presented as part of the current runtime architecture until the group deploys and verifies them.
