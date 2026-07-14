---
title: "Record RDS Connection Settings"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Objective

Record the RDS values required by later EC2 and Spring Boot steps without exposing the real endpoint or password and without attempting an EC2 connection before EC2 exists.

## Prerequisites

- `techblog-db` is **Available** in `ap-southeast-1`.
- Database `techblog` and port `3306` were configured.
- The RDS security group references `<EC2_SECURITY_GROUP_ID>`.

## Steps

1. Open **Amazon RDS → Databases → techblog-db**.
2. In **Connectivity & security**, record the endpoint outside the public report.
3. In workshop text and screenshots, replace it with `<RDS_ENDPOINT>`.
4. Record database `techblog`, port `3306`, and username `admin`.
5. Keep the real password in the protected deployment workflow and use `<DB_PASSWORD>` in all documentation.
6. Do not run `ssh`, `mysql`, `scp`, or import commands on this page.

## Configuration

| Item | Safe report value |
|---|---|
| Host | `<RDS_ENDPOINT>` |
| Port | `3306` |
| Database | `techblog` |
| Username | `admin` |
| Password | `<DB_PASSWORD>` |
| Public access | No |

The future JDBC value is documented safely as:

```text
jdbc:mysql://<RDS_ENDPOINT>:3306/techblog
```

## Verification

Confirm these values in the RDS console and confirm the selected security group is `<RDS_SECURITY_GROUP_ID>`. Connectivity is verified later from EC2 in 5.5.3.

## Expected Result

The team has a complete, redacted connection-value checklist for the deployment stage. No EC2 runtime action has been performed prematurely.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Endpoint is copied incorrectly | Copy it again from **Connectivity & security**, then keep only `<RDS_ENDPOINT>` in the report. |
| Password appears in a note or screenshot | Remove/redact it immediately and use `<DB_PASSWORD>`. |
| Connection cannot be tested | Expected until EC2 is launched; continue with the local export step. |

## Cost and Security Notes

Reading configuration has no additional charge. Never publish the full endpoint, password, AWS Account ID, or security-group identifiers.

<!-- TODO: Add /images/5-Workshop/5.4-RDS-MySQL/rds-connection-settings.png. Caption: *Figure 5.4.3.1. Redacted RDS connection settings recorded before EC2 creation.* -->
