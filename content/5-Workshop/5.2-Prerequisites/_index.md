---
title: "Prerequisites"
date: 2026-07-10
weight: 2
pre: " <b> 5.2. </b> "
---

Prepare an AWS account with permissions for Budgets, EC2, RDS, S3, IAM, CloudWatch, SNS, CloudFront, and WAF. Locally, install Git and Java 17, and confirm the Maven Wrapper can build TechBlog.

Before creating AWS resources, test registration, login, Reader/Writer/Admin authorization, post approval, comments, and image uploads. Externalize `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`, `AWS_REGION`, and `S3_BUCKET`; never commit real credentials.

Use one AWS Region, tag resources with `Project=TechBlog`, choose a globally unique bucket name, and create an AWS Budget first.

{{% notice warning %}}
Do not use the root account for daily work, and never commit AWS access keys or database passwords.
{{% /notice %}}
