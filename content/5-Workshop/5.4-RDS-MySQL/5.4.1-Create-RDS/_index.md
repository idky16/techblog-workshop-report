---
title: "Create RDS MySQL"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Objective

Create a private Amazon RDS for MySQL database for TechBlog.

## Prerequisites

- Region is `ap-southeast-1`.
- The team has decided the database name is `techblog`.

## Steps

1. Open **Amazon RDS**.
2. Create a MySQL database.
3. Set DB identifier to `techblog-db`.
4. Select instance class `db.t3.micro`.
5. Use Single-AZ for the demo.
6. Set **Publicly accessible** to **No**.
7. Set database name to `techblog`.
8. Use port `3306`.
9. Set master user to `admin`.
10. Wait until status is **Available**.

## Configuration

| Setting | Value |
|---|---|
| DB identifier | `techblog-db` |
| Engine | MySQL |
| Instance class | `db.t3.micro` |
| Availability | Single-AZ |
| Public access | No |
| Database name | `techblog` |
| Port | `3306` |
| Master user | `admin` |

## Verification

Record the endpoint as `<RDS_ENDPOINT>` in the report. Do not paste the full endpoint if it exposes account-specific details.

## Expected Result

RDS reaches **Available** and is ready for security group configuration.

## Common Issues

| Issue | Fix |
|---|---|
| RDS takes time to create | Wait until status becomes **Available**. |
| Wrong Region | Re-check `ap-southeast-1`. |

## Cost and Security Notes

Single-AZ and `db.t3.micro` reduce cost for the demo. Keep public access disabled.

<!-- TODO: Add /images/5-Workshop/5.4-RDS-MySQL/rds-available.png. Caption: *Figure 5.4.1.1. techblog-db in Available state with public access disabled.* -->
