---
title: "Test, monitor, and troubleshoot"
date: 2026-07-10
weight: 6
pre: " <b> 5.6. </b> "
---

Test every Reader, Writer, and Admin workflow through the public endpoint. Confirm business records in RDS, objects under the correct S3 prefixes, and correct sessions, CSRF handling, redirects, and uploads through CloudFront/WAF.

Send application logs to CloudWatch Logs with a limited retention period. Create useful EC2 and RDS alarms, connect them to an SNS email subscription, and trigger a controlled test alarm.

For failures, inspect Spring Boot logs and environment variables; verify the RDS security-group source and port 3306; verify the EC2 role, S3 ARN, prefix, and Region; inspect CloudFront cookie/header forwarding; and review WAF sampled requests. Finish by checking Billing and AWS Budgets.
