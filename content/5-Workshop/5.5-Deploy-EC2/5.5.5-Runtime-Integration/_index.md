---
title: "Configure Runtime Integration for RDS, S3, and SES"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5.5. </b> "
---

## Objective

Provide TechBlog with the RDS, S3, SES, Region, and log-file settings required on EC2 without hard-coding credentials in the JAR or repository.

## Prerequisites

- `/home/ec2-user/techblog.jar` exists.
- `techblog-ec2-role` is attached to EC2.
- RDS is reachable from EC2.
- S3 bucket and SES identity exist in `ap-southeast-1`.

## Steps

Create a protected configuration directory and application log directory:

```bash
sudo install -d -m 700 /etc/techblog
sudo install -d -o ec2-user -g ec2-user -m 750 /var/log/techblog
sudo touch /etc/techblog/techblog.env
sudo chmod 600 /etc/techblog/techblog.env
```

Edit `/etc/techblog/techblog.env` with values that match the application's property binding:

```bash
DB_URL=jdbc:mysql://<RDS_ENDPOINT>:3306/techblog
DB_USERNAME=admin
DB_PASSWORD=<DB_PASSWORD>
AWS_REGION=ap-southeast-1
TECHBLOG_STORAGE_PROVIDER=s3
TECHBLOG_S3_BUCKET=techblog-uploads-ttv-2026
TECHBLOG_PRESIGN_TTL_MINUTES=10
TECHBLOG_SES_ENABLED=true
TECHBLOG_SES_FROM_EMAIL=<SES_VERIFIED_SENDER_EMAIL>
TECHBLOG_SES_ADMIN_RECIPIENT=<SES_VERIFIED_ADMIN_EMAIL>
LOGGING_FILE_NAME=/var/log/techblog/application.log
```

Never include the real environment file in the report. If the source code uses different environment-variable names, use the exact names from the current configuration class and update both language pages consistently.

## Configuration

| Variable | Purpose | Secret |
|---|---|---|
| `DB_URL` | JDBC endpoint for private RDS | No, but keep the real endpoint out of screenshots |
| `DB_USERNAME` | RDS application/master user used by the demo | No |
| `DB_PASSWORD` | RDS password | Yes |
| `AWS_REGION` | SDK Region | No |
| `TECHBLOG_STORAGE_PROVIDER` | Selects S3 instead of the default local provider | No |
| `TECHBLOG_S3_BUCKET` | S3 object-storage bucket | No |
| `TECHBLOG_PRESIGN_TTL_MINUTES` | Presigned GET lifetime; value `10` | No |
| `TECHBLOG_SES_ENABLED` | Enables the SES notification component | No |
| `TECHBLOG_SES_FROM_EMAIL` | Verified SES sender | Personal/identity data; redact it |
| `TECHBLOG_SES_ADMIN_RECIPIENT` | Verified Sandbox administrator recipient | Personal/identity data; redact it |
| `LOGGING_FILE_NAME` | Spring Boot application log file | No |

AWS SDK for Java should use the default credential provider chain. Do not add `AWS_ACCESS_KEY_ID` or `AWS_SECRET_ACCESS_KEY`.

## Application Integration Behavior

- Avatar markers use `s3:avatars/<uuid>.<ext>`; post-image markers use `s3:posts/<uuid>.<ext>`.
- MySQL stores markers, not image binary data or presigned URLs; legacy local image references remain supported.
- Replacing or deleting managed media performs the corresponding S3 object cleanup.
- Private images are rendered with presigned GET URLs that expire after 10 minutes.
- After registration is saved, SES sends an administrator notification containing only username, display name when available, registration email, and registration time. A mail failure must not roll back the new user.
- Application logging writes suitable INFO/WARN/ERROR events to `/var/log/techblog/application.log` without passwords, tokens, cookies, or sensitive content.

## Verification

Check permissions without printing file contents:

```bash
sudo stat -c '%a %U:%G %n' /etc/techblog/techblog.env
sudo ls -ld /var/log/techblog
```

Expected environment-file mode is `600`. Runtime integration is fully verified in module 5.6 through object, email, and log tests.

## Expected Result

The EC2 runtime has all required integration values, the log path is writable by the service user, and no static AWS credential is present.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Spring property is empty | Compare the environment name with the current `application.properties` or configuration class. |
| S3/SES client reports no Region | Confirm `AWS_REGION=ap-southeast-1` and restart the service. |
| Upload still writes locally | Confirm `TECHBLOG_STORAGE_PROVIDER=s3` is present in the active environment file. |
| SES component is inactive | Confirm `TECHBLOG_SES_ENABLED=true` and both verified email values are set. |
| Application cannot write log file | Check ownership and permission of `/var/log/techblog`. |
| Secret appears in shell history | Avoid inline export commands containing real passwords; edit the restricted file carefully. |

## Cost and Security Notes

The environment file is acceptable for the learning demo when restricted to root access. AWS Secrets Manager or Parameter Store remains a future improvement.

<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/runtime-config-redacted.png. Caption: *Figure 5.5.5.1. Redacted runtime variable names and protected file permission.* -->
