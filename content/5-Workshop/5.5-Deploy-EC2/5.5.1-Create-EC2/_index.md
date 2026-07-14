---
title: "Create EC2 and Configure Security Group"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Objective

Launch the EC2 instance that runs the TechBlog Spring Boot JAR.

## Prerequisites

- IAM Role `techblog-ec2-role` exists.
- The application security group and RDS reference were prepared in 5.4.2.

## Steps

1. Open **Amazon EC2**.
2. Launch instance named `techblog-ec2`.
3. Choose Amazon Linux 2023.
4. Choose instance type `t3.micro`.
5. Use 8 GiB gp3 storage.
6. Enable auto-assign public IP for the demo.
7. Attach IAM instance profile `techblog-ec2-role`.
8. Assign the application security group prepared in 5.4.2 and confirm:
   - SSH `22` from `<MY_PUBLIC_IP>`.
   - TCP `8080` public for the demo.
   - HTTP `80` is not required because Nginx is not deployed.
9. Do not open MySQL on EC2.

## Configuration

| Setting | Value |
|---|---|
| Name | `techblog-ec2` |
| AMI | Amazon Linux 2023 |
| Instance type | `t3.micro` |
| Storage | 8 GiB gp3 |
| IAM role | `techblog-ec2-role` |
| App port | `8080` |

## Verification

Confirm the instance is running, the prepared security group is assigned, and `techblog-ec2-role` appears in instance details. This is the first point in the workshop where the role is attached to EC2.

## Expected Result

EC2 is ready for SSH and Java installation.

## Common Issues

| Issue | Fix |
|---|---|
| SSH timeout | Check public IP and inbound rule for `<MY_PUBLIC_IP>`. |
| Port 8080 unreachable later | Add TCP `8080` inbound rule for demo access. |

## Cost and Security Notes

Port `8080` is public only for demo and grading access. Close or restrict it after the demo.

<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/ec2-running.png. Caption: *Figure 5.5.1.1. techblog-ec2 running with techblog-ec2-role.* -->
<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/ec2-security-group.png. Caption: *Figure 5.5.1.2. Demo inbound rules with SSH restricted and TCP 8080 public.* -->
