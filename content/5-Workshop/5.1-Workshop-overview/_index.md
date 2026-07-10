---
title: "Workshop overview"
date: 2026-07-10
weight: 1
pre: " <b> 5.1. </b> "
---

## Objectives

You will migrate TechBlog from localhost to AWS, run its Spring Boot JAR on EC2, connect it to RDS MySQL, move uploaded images to S3, protect credentials, test all application roles, and configure monitoring and cost alerts.

TechBlog remains a monolithic MVC application. Thymeleaf pages and business logic run together, so this workshop does not introduce a React SPA, API Gateway, or Elastic Beanstalk.

The target flow is **User → CloudFront + WAF → EC2 Spring Boot → RDS MySQL + S3**. RDS accepts MySQL traffic only from the EC2 security group, while EC2 accesses the private S3 bucket through an IAM role.

Successful completion means the application is reachable, business data persists in RDS, uploads persist in S3, no static AWS keys or database passwords exist in source control, and CloudWatch/SNS monitoring works.
