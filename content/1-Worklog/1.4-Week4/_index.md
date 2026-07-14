---
title: "Week 4"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

## Time

11/05/2026 - 17/05/2026

## Weekly Objectives

- Design an AWS architecture that matches the Spring Boot MVC monolith.
- Keep the first demo simple, secure, and cost-aware.

## Work Completed

- Selected `ap-southeast-1` as the deployment Region.
- Designed the main path: browser → Internet Gateway → public EC2 port `8080` → private RDS MySQL port `3306`.
- Added private S3, an EC2 IAM Role, Amazon SES, CloudWatch Logs, one CPU alarm, and AWS Budgets to the project workflow.
- Compared cost and complexity, then kept the demo architecture limited to the AWS resources required by TechBlog.

## Results and Lessons

- Completed a workflow diagram that accurately represents the deployed components and integration boundaries.
- Chose small EC2/RDS configurations and Single-AZ for the learning environment.
- Learned to separate implemented architecture from future improvements in technical documentation.
