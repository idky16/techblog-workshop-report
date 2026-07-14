---
title: "Create and Prepare the EC2 IAM Role"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

## Objective

Create `techblog-ec2-role` and prepare the policies used by the final deployment. EC2 does not exist yet, so this page does not attach the role to an instance or run AWS CLI commands on EC2.

## Prerequisites

- S3 bucket `techblog-uploads-ttv-2026` exists.
- The required S3 and CloudWatch permissions are known.
- The SES identity ARN will be available after 5.3.4; use `<SES_VERIFIED_IDENTITY_ARN>` until then.

## Steps

1. Open **AWS IAM → Roles → Create role**.
2. Select **AWS service** as the trusted entity and **EC2** as the use case.
3. Name the role `techblog-ec2-role`.
4. Attach managed policy `AmazonS3FullAccess`, which is the policy currently used by the verified demo.
5. Attach managed policy `CloudWatchAgentServerPolicy` for CloudWatch Agent.
6. Prepare inline policy `TechBlogSesSendEmail` with only `ses:SendEmail` and `ses:SendRawEmail`. Keep `<SES_VERIFIED_IDENTITY_ARN>` as a placeholder until 5.3.4 provides the verified identity.
7. Save the role. Do not attach it to an instance yet.
8. Record that the application will use the AWS SDK default credential provider chain; no IAM user Access Key is created.

## Current Configuration

| Item | Verified value |
|---|---|
| Role | `techblog-ec2-role` |
| Trusted service | Amazon EC2 |
| S3 managed policy | `AmazonS3FullAccess` |
| CloudWatch managed policy | `CloudWatchAgentServerPolicy` |
| SES inline policy | `TechBlogSesSendEmail` |
| SES actions | `ses:SendEmail`, `ses:SendRawEmail` |
| Static credentials | Not used |

`AmazonS3FullAccess` is broader than TechBlog needs. It is recorded because it is the real demo configuration, not because it is the recommended final policy. A future hardening task is to replace it with `ListBucket` on the TechBlog bucket and `GetObject`, `PutObject`, and `DeleteObject` only for `avatars/*` and `posts/*`.

## Verification

1. Review **Trust relationships** and confirm the EC2 service principal.
2. Review attached policies and the `TechBlogSesSendEmail` inline policy.
3. Confirm the role exists and no access key was created for it.
4. After 5.3.4, replace `<SES_VERIFIED_IDENTITY_ARN>` and review the final inline policy in the IAM console.
5. Defer role attachment and S3/SES/CloudWatch runtime tests to modules 5.5 and 5.6.

## Expected Result

`techblog-ec2-role` is ready to be attached when EC2 is launched in 5.5.1. Its final policy set matches the deployed state, no static key exists, and no runtime success is claimed at this stage.

## Troubleshooting

| Issue | Resolution |
|---|---|
| EC2 is unavailable in this step | Expected: EC2 is created later in 5.5. Do not create a temporary instance only to test the role. |
| SES policy cannot be finalized | Complete 5.3.4, then replace `<SES_VERIFIED_IDENTITY_ARN>` with the verified identity ARN. |
| Wrong trusted entity | Edit the trust policy so the EC2 service can assume the role. |
| S3 policy is described as least privilege | Correct the report: the deployed demo currently uses broad `AmazonS3FullAccess`; narrowing is future work. |

## Cost and Security Notes

IAM has no direct service charge. Do not solve an authorization problem by adding long-lived keys. Narrow `AmazonS3FullAccess` only after the required object operations are confirmed and tested.

<!-- TODO: Add /images/5-Workshop/5.3-Budget-S3-IAM/iam-role-policies.png. Caption: *Figure 5.3.3.1. Redacted techblog-ec2-role trust relationship and prepared policies before EC2 creation.* -->
