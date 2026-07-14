---
title: "Verify Budget, S3, IAM, and SES"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.3.5. </b> "
---

## Objective

Perform one foundation review before creating the database and application runtime. This check prevents later failures from being incorrectly diagnosed as Spring Boot bugs.

## Prerequisites

- AWS Budget exists.
- S3 bucket `techblog-uploads-ttv-2026` exists.
- IAM Role `techblog-ec2-role` exists.
- SES sender identity is verified in `ap-southeast-1`.

## Steps

1. Open AWS Budgets and confirm the cost budget and notification threshold.
2. Open the S3 bucket and review Block Public Access, Object Ownership, SSE-S3, Region, and prefixes.
3. Open `techblog-ec2-role` and review its trust relationship.
4. Record that current S3 access is `AmazonS3FullAccess`; do not label it least privilege.
5. Confirm inline policy `TechBlogSesSendEmail` allows only `ses:SendEmail` and `ses:SendRawEmail` for the verified SES identity.
6. Confirm `CloudWatchAgentServerPolicy` is attached for CloudWatch Agent/Logs.
7. Open Amazon SES and confirm sender status **Verified**.
8. Record the SES Sandbox state and confirm the administrator test recipient is verified.
9. Confirm in IAM that the role has no long-lived access key; role credentials are temporary and will be obtained only after attachment in 5.5.1.
10. Capture console screenshots only after redacting account ID and email data. Do not run EC2, AWS CLI, SDK, S3 object, SES send, or CloudWatch Agent tests on this page.

## Configuration Checklist

| Item | Expected value |
|---|---|
| Budget | Active notification rule |
| S3 Region | `ap-southeast-1` |
| S3 access | Private, Block Public Access on |
| S3 prefixes | `avatars/`, `posts/` |
| IAM Role | `techblog-ec2-role`, trusted by EC2 |
| Current S3 policy | `AmazonS3FullAccess` (future narrowing required) |
| SES identity | Verified |
| SES account state | Sandbox; verified recipients only |
| CloudWatch destination | `/techblog/application` |
| Static Access Key | None |

## Verification

Use this decision rule:

- A console configuration is verified by its redacted console view.
- An IAM permission is verified only after the EC2 application or agent successfully uses it.
- S3 integration is verified later by create/replace/delete, database-marker, and 10-minute presigned-URL tests.
- SES integration is verified later by console send, IAM Role CLI send, and administrator notification after registration.

This avoids presenting an untested policy as successful application behavior.

## Expected Result

All foundation settings are internally consistent and ready for RDS/EC2 deployment. This result covers console configuration only; runtime evidence is collected after EC2 is created.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Bucket or SES is in the wrong Region | Recreate or verify in `ap-southeast-1` and update runtime values. |
| S3 policy is broader than required | Record the current state and plan a tested replacement scoped to the TechBlog bucket and prefixes. |
| SES identity is pending | Complete verification before runtime testing. |
| An access key was created for application use | Delete it and use the EC2 instance profile after 5.5.1. |

## Cost and Security Notes

This review has no significant direct cost. Never publish the Budget recipient email, SES sender, AWS Account ID, or policy details containing a real identity ARN.

<!-- TODO: Add /images/5-Workshop/5.3-Budget-S3-IAM/foundation-verification.png. Caption: *Figure 5.3.5.1. Redacted verification of Budget, S3, IAM, and SES.* -->
