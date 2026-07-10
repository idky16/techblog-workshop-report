---
title: "Clean up resources"
date: 2026-07-10
weight: 7
pre: " <b> 5.7. </b> "
---

Before deletion, preserve required screenshots and logs, export or snapshot only the data you need, download required S3 objects, and document the configuration.

Disable and delete CloudFront, detach and delete WAF resources, terminate EC2 and unused storage/IP resources, delete RDS and unnecessary snapshots, empty and delete the S3 bucket including versions, and remove workshop-specific secrets, parameters, IAM roles, logs, alarms, SNS resources, and security groups.

Finally, search all used Regions and resources tagged `Project=TechBlog`, then inspect Billing and Cost Explorer for leftover RDS snapshots, EBS volumes, Elastic IPs, S3 versions, logs, CloudFront, or WAF resources.
