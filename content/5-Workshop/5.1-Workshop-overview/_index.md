---
title: "Workshop Overview"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

{{% notice info %}}
### Access the live demo

The mentor can access the TechBlog website running on AWS at:

[http://13.212.52.148:8080](http://13.212.52.148:8080)

The website is served directly by Spring Boot on Amazon EC2 over HTTP port 8080. The current demo does not use a domain or HTTPS and is expected to remain available until July 30, 2026.
{{% /notice %}}

## Workshop Overview

This workshop explains how group TTV deploys TechBlog from local IntelliJ/Laragon to AWS while preserving its Spring Boot MVC monolith. Thymeleaf pages, controllers, security configuration, business services, repositories, and persistence logic remain in one executable JAR. The workshop does not redesign the system as a React SPA, API Gateway application, or separate frontend/backend deployment.

The deployed learning environment uses one public EC2 instance for the application and one private RDS MySQL instance for relational data. S3 stores avatar and post-image objects, Amazon SES sends the administrator registration notice, and CloudWatch centralizes application logs and monitors the CPU alarm. Users reach the website temporarily through:

```text
http://<EC2_PUBLIC_IP>:8080/
```

AWS Budgets is created before chargeable resources. EC2 uses `techblog-ec2-role` for AWS SDK and CloudWatch Agent calls; no static AWS credential is placed in the project.

## Target Results

| Result | Verifiable state |
|---|---|
| EC2 runtime | `techblog-ec2` runs Amazon Linux 2023 and Java 17 Amazon Corretto. |
| Application process | The executable JAR runs as `techblog.service` and remains active after SSH closes. |
| Database | Private `techblog-db` stores database `techblog` and the imported application tables. |
| Public access | `curl -I http://localhost:8080` and the browser return HTTP 200. |
| Authorization | Guest, USER/Reader, EDITOR/Writer, and ADMIN scenarios are exercised. |
| Object storage | Avatar and post images use `avatars/` and `posts/` in private bucket `techblog-uploads-ttv-2026`. |
| Database reference | MySQL stores `s3:avatars/<uuid>.<ext>` or `s3:posts/<uuid>.<ext>`, not image binary content or presigned URLs. |
| Private image delivery | New S3 images render through presigned GET URLs with a 10-minute lifetime; legacy local images still work. |
| Transactional email | After registration is saved, Amazon SES sends a best-effort notice to the configured verified administrator recipient. |
| Centralized logs | CloudWatch Agent publishes appropriate events from `/var/log/techblog/application.log` to Standard-class `/techblog/application` with 14-day retention. |
| Alarm | `techblog-high-cpu` monitors Average CPUUtilization greater than 70% for 2 of 2 five-minute periods without a notification action. |
| Network boundary | RDS port `3306` accepts only the EC2 security group. |
| Upload validation | The 2.09 MB avatar regression case succeeds. Spring's multipart transport limit is 50 MB per file/request, while application rules limit avatars to 5 MiB and post images to 15 MiB. |

Actual console states, object keys, log-stream names, email delivery, and alarm states must be demonstrated with redacted evidence; this page does not invent those values.

## Deployed Architecture

Solid arrows show active request, database, object, email, and log paths. The IAM attachment is shown as a dashed relationship because it grants credentials rather than carrying application data. Budgets is intentionally outside the runtime request path.

{{< mermaid align="center" >}}
flowchart LR
    U[Reader / Writer / Admin<br/>Browser] -->|HTTP :8080| G[Internet Gateway]
    subgraph VPC[Amazon VPC]
        subgraph PUB[Public Subnet]
            E[Amazon EC2<br/>techblog-ec2<br/>Spring Boot + systemd]
        end
        subgraph DBNET[Private database path]
            R[(Amazon RDS for MySQL<br/>techblog-db<br/>techblog)]
        end
        G --> E
        E -->|JDBC :3306| R
    end
    E -. instance profile .-> I[AWS IAM Role<br/>techblog-ec2-role]
    E -->|AWS SDK<br/>put / get / delete| S[(Amazon S3<br/>avatars/ and posts/)]
    E -->|AWS SDK<br/>admin registration notice| M[Amazon SES<br/>Sandbox]
    E -->|CloudWatch Agent<br/>application.log| L[CloudWatch Logs<br/>/techblog/application]
    E -->|CPUUtilization| A[CloudWatch Alarm<br/>techblog-high-cpu]
    B[AWS Budgets] --> O[Shared-account cost alert]
{{< /mermaid >}}

Architecture interpretation:

- The main path is `User → Internet Gateway → EC2 Spring Boot → RDS MySQL`.
- S3, SES, CloudWatch, IAM, and Budgets are AWS services outside the EC2/RDS subnet drawing.
- The private S3 bucket is not static website hosting. Spring Boot uses AWS SDK for object operations.
- The implemented media resolver creates 10-minute presigned GET URLs for `s3:` references and preserves legacy local URLs.
- Upload validation checks JPEG, PNG, GIF, or WebP file signatures; SVG and disguised non-image files are rejected.
- Amazon SES failure must be handled outside the database registration transaction so a temporary mail error does not roll back a valid user account.
- The CloudWatch alarm is reviewed in the CloudWatch console. The report does not claim SNS or SES sends an alarm notification.

## Workshop Deployment Flow

| Step | Activity | Verification point |
|---:|---|---|
| 1 | Verify local Java, Maven Wrapper, MySQL, uploads, and roles | TechBlog works at `http://localhost:8080`. |
| 2 | Create AWS Budgets | Cost alert exists before resource provisioning. |
| 3 | Create and secure S3 | Private bucket, encryption, and two prefixes are confirmed. |
| 4 | Create and prepare the IAM Role | The trust relationship and policy set exist; no instance attachment or runtime test occurs yet. |
| 5 | Verify Amazon SES identity | Sender identity is verified and Sandbox status is recorded. |
| 6 | Create RDS MySQL | `techblog-db` reaches `Available` with public access disabled. |
| 7 | Prepare EC2-RDS security groups and local migration data | RDS `3306` references the prepared EC2 SG, safe connection placeholders are recorded, and `techblog.sql` exists locally. |
| 8 | Launch EC2, attach the IAM Role, and install runtime tools | Amazon Linux 2023 has `techblog-ec2-role`, Java 17, MariaDB client, and CloudWatch Agent. |
| 9 | Connect to RDS and import the database | Nine TechBlog tables are visible on RDS. |
| 10 | Build and upload JAR | `techblog-0.0.1-SNAPSHOT.jar` is copied to EC2. |
| 11 | Configure RDS, S3 provider/bucket/TTL, SES sender/admin recipient, and log values | Restricted environment file contains placeholders resolved on EC2. |
| 12 | Run `systemd` | Service is active and local HTTP check returns 200. |
| 13 | Configure CloudWatch Logs and the CPU alarm | New log events and the `techblog-high-cpu` definition appear in the console. |
| 14 | Test roles, S3, SES, and upload limits | Functional evidence is collected without exposing secrets. |
| 15 | Review security and troubleshoot | Known deployment failures have documented causes and fixes. |
| 16 | Clean up | Unneeded chargeable resources are removed or intentionally retained. |

## AWS Services in Scope

| Service | Workshop state | Purpose |
|---|---|---|
| AWS Budgets | Configured | Controls spending for the shared TTV environment. |
| Amazon EC2 | Deployed | Runs the Spring Boot MVC JAR and CloudWatch Agent. |
| Amazon RDS for MySQL | Deployed privately | Stores TechBlog relational data. |
| Amazon S3 | Integrated application storage | Stores avatar and post-image objects in a private bucket. |
| AWS IAM Role | Attached to EC2 | Uses `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy`, and inline `TechBlogSesSendEmail`; no static keys are used. |
| Amazon SES | Implemented in Sandbox | Sends an administrator registration notice from a verified email identity; failure does not roll back registration. |
| Amazon CloudWatch Logs | Implemented | Stores application events in Standard-class `/techblog/application` for 14 days. |
| Amazon CloudWatch Alarm | Implemented, no action | `techblog-high-cpu` monitors sustained CPU and is reviewed in the console. |

## Step 1: Observe the Architecture

Trace one browser request to EC2, one JDBC request to RDS, one image operation to S3, one registration-email call to SES, and one application-log event to CloudWatch. This makes the diagram defendable because every arrow maps to a workshop verification step.

<figure>
  {{< content-image src="images/5-Workshop/Techblog_Workflow_AWS.png" alt="Implemented TechBlog workflow on AWS" >}}
  <figcaption><em>Figure 5.1.1. Implemented TechBlog workflow connecting EC2, RDS, S3, Amazon SES, CloudWatch, and the EC2 IAM Role.</em></figcaption>
</figure>

## Step 2: Identify the AWS Services

Open the Resource Groups/console views used by the team and record only resources that belong to TechBlog. Hide account IDs, email addresses, public IPs, endpoints, cookies, and credentials before adding screenshots.

<!-- TODO: Add /images/5-Workshop/5.1-Workshop-overview/aws-services-overview.png. Caption: *Figure 5.1.2. AWS services used by the TechBlog workshop.* -->

## Demo Environment Limitations

- The website uses HTTP through public IP and port `8080`; there is no HTTPS or domain.
- The EC2 public IP may change after stop/start.
- EC2 and RDS use a small, Single-AZ learning configuration.
- There is no Nginx, Route 53, ALB, Auto Scaling, Multi-AZ, CloudFront, WAF, or NAT Gateway.
- The S3 bucket is private; images are displayed through 10-minute presigned GET URLs and legacy local images remain supported.
- SES remains in Sandbox, so the configured administrator recipient must be verified; TechBlog does not send this notice to arbitrary registration addresses.
- `techblog-high-cpu` is console-monitored because SNS notification is not part of this workshop.
- Database credentials remain in a mode-`600` environment file for the demo; Secrets Manager or Parameter Store is a future improvement.
- The environment is suitable for learning and demonstration, not described as production-ready.

## Team Responsibilities

| Member | Main responsibility | Deliverables |
|---|---|---|
| Member 1 | AWS Budgets, IAM Role, S3 infrastructure, and S3 testing | Budget evidence, bucket settings, current policy review, future S3 permission narrowing, and object lifecycle tests. |
| Member 2 | RDS MySQL, security groups, migration, and data verification | Private RDS, SG reference, database import, nine-table check, and S3-key reference check. |
| Member 3 | EC2, Java, runtime configuration, systemd, and SES | Runtime deployment, safe environment values, service evidence, SES identity, CLI send, and administrator-notification test. |
| Member 4 | Functional testing, CloudWatch, security, and troubleshooting | Role matrix, centralized logs, CPU alarm, HTTP 413 regression, security review, and known-error table. |

Shared work:

- All four members review 5.1 and 5.2.
- All four members agree on section 2-Proposal.
- All four members perform the 5.7 cleanup checklist.
- The group uses one AWS account, does not share root credentials, and creates separate least-privilege identities if more members require console access.

## Expected Result

The reader can explain the complete TechBlog deployment, distinguish the main request path from AWS integrations, identify the security boundary of each service, and follow the remaining modules without relying on architecture from an unrelated sample project.
