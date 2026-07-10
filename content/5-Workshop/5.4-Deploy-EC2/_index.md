---
title: "Deploy TechBlog on Amazon EC2"
date: 2026-07-10
weight: 4
pre: " <b> 5.4. </b> "
---

Create an EC2 IAM role that can read only the selected secret, write CloudWatch logs, and access only the TechBlog bucket. Launch a small Linux EC2 instance in the RDS VPC, attach `techblog-ec2-sg` and the IAM role, and prefer Systems Manager over unrestricted SSH.

Build the application with `./mvnw clean package -DskipTests`, install Java 17 on EC2, upload the JAR, and provide `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`, `AWS_REGION`, and `S3_BUCKET`. Retrieve secrets at startup and use `systemd` for process supervision; do not place static AWS keys on the instance.

Initially test `http://<ec2-public-ip>:8080`. Confirm startup, schema creation, and authentication before introducing CloudFront and WAF.
