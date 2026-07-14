---
title: "Foundation Services"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
alwaysopen: true
---

## Module Objective

Create the shared-account cost guardrail and the AWS integrations required before application deployment: private S3 object storage, an EC2 IAM Role, and an Amazon SES verified sender identity. These services belong to the same TechBlog deployment plan and are not separate sample architectures.

## Module Flow

{{< mermaid align="center" >}}
flowchart TB
    B[AWS Budgets<br/>cost guardrail]
    S[(Amazon S3<br/>private bucket)]
    I[AWS IAM Role<br/>role and policies prepared]
    M[Amazon SES<br/>verified email identity]
    I -. future attachment in 5.5 .-> E[Amazon EC2<br/>not created in this module]
{{< /mermaid >}}

## Child Pages

1. [Create AWS Budget](5.3.1-budget/)
2. [Create and Secure the S3 Bucket](5.3.2-s3/)
3. [Create and Prepare the EC2 IAM Role](5.3.3-iam-role/)
4. [Configure an Amazon SES Verified Identity](5.3.4-ses/)
5. [Verify Budget, S3, IAM, and SES](5.3.5-verify/)

## Module Configuration

| Item | Required value |
|---|---|
| Region | `ap-southeast-1` |
| S3 bucket | `techblog-uploads-ttv-2026` |
| S3 access | Private, Block Public Access on, ACLs disabled |
| S3 encryption | SSE-S3 |
| S3 prefixes | `avatars/`, `posts/` |
| IAM Role | `techblog-ec2-role` trusted by EC2 |
| Current attached policies | `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy` |
| SES inline policy | `TechBlogSesSendEmail` |
| SES sender | `<SES_VERIFIED_SENDER_EMAIL>` |
| SES state | Sandbox; verified recipients only |
| Static credentials | Not used |

## Expected Result

The Budget alert is active, the private bucket and prefixes exist, the actual IAM policies are documented, and the SES email identity is verified in Sandbox. `AmazonS3FullAccess` is deliberately identified as broader than required and must not be presented as least privilege. Real account IDs, email addresses, and ARNs remain hidden in the report.

## Cost and Security Notes

AWS Budgets only alerts; it does not stop resources. S3 has no Transfer Acceleration or public website configuration. SES uses no Dedicated IP, Mail Manager, Global Endpoint, or attachment workflow. The IAM Role is the application credential source.
