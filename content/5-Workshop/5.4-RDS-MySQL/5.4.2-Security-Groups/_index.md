---
title: "Configure EC2-RDS Security Groups"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Objective

Prepare the EC2-to-RDS security-group reference before launching the EC2 instance, without exposing RDS to the internet.

## Prerequisites

- RDS is available and its security group can be edited.
- No EC2 instance is required yet.

## Steps

1. In the VPC/EC2 console, create the application security group that will later be assigned to `techblog-ec2`; record it as `<EC2_SECURITY_GROUP_ID>`.
2. Open or create the RDS security group and record it as `<RDS_SECURITY_GROUP_ID>`.
3. Add the RDS inbound rule:
   - Type: MySQL/Aurora
   - Port: `3306`
   - Source: `<EC2_SECURITY_GROUP_ID>`
4. Associate `<RDS_SECURITY_GROUP_ID>` with `techblog-db`.
5. Do not use `0.0.0.0/0` for RDS. SSH and application-port rules are completed when EC2 is launched in 5.5.1.

## Configuration

| Rule | Value |
|---|---|
| Protocol | TCP |
| Port | `3306` |
| Source | `<EC2_SECURITY_GROUP_ID>` |
| Public source | Not allowed |

## Verification

Check in the console that the RDS inbound rule references `<EC2_SECURITY_GROUP_ID>`, not a public CIDR. A network connection test is not possible until 5.5.3.

## Expected Result

The security-group boundary is ready. When `techblog-ec2` is launched in 5.5.1, assigning the prepared group makes it eligible to reach RDS on port `3306`.

## Common Issues

| Issue | Fix |
|---|---|
| EC2 instance is not listed | Expected at this stage; reference the prepared security group, not an instance. |
| Wrong source type | Select the security group ID rather than a CIDR. |

## Cost and Security Notes

Security group reference is safer than opening database access to the internet.

<!-- TODO: Add /images/5-Workshop/5.4-RDS-MySQL/rds-security-group.png. Caption: *Figure 5.4.2.1. RDS port 3306 restricted to the EC2 security group.* -->
