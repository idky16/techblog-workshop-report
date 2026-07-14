---
title: "Security Review and Troubleshooting"
date: 2026-07-10
weight: 7
chapter: false
pre: " <b> 5.6.7. </b> "
---

## Objective

Complete a final security review and consolidate the real deployment failures with the new S3, SES, and CloudWatch checks.

## Prerequisites

- EC2, RDS, S3, SES, `systemd`, CloudWatch Logs, and alarm configuration have been reviewed.
- Functional and integration tests have recorded pass/fail results.

## Steps

Review each boundary:

1. **AWS account:** root credentials are not shared or used daily; members use separate least-privilege identities if needed.
2. **EC2:** SSH `22` accepts only `<MY_PUBLIC_IP>`; TCP `8080` is public only for the demo window.
3. **RDS:** public access is disabled; `3306` accepts only the EC2 security group.
4. **S3:** Block Public Access is on, ACLs are disabled, and the application does not expose permanent public object URLs.
5. **IAM:** `techblog-ec2-role` is attached; there are no static keys. `CloudWatchAgentServerPolicy` and `TechBlogSesSendEmail` support their verified functions, while current `AmazonS3FullAccess` is flagged for future narrowing.
6. **SES:** the verified email identity and Sandbox rule are respected; the administrator notice contains no password, hash, JWT, or secret.
7. **Logs:** local and CloudWatch logs exclude passwords, tokens, cookies, authorization headers, and sensitive user content.
8. **Runtime secrets:** `/etc/techblog/techblog.env` is mode `600` and excluded from Git/screenshots.
9. **Demo limitations:** access is HTTP, without Nginx, HTTPS, domain, CloudFront, WAF, ALB, or Auto Scaling.

## Troubleshooting Table

| Issue | Likely cause | Resolution |
|---|---|---|
| SSH timeout | The user's public IP changed | Update the SSH inbound source to `<MY_PUBLIC_IP>`. |
| PEM bad permissions | Windows OpenSSH rejects broad ACLs | Run the documented `icacls` commands. |
| Unknown MySQL server host | One RDS endpoint character is wrong | Re-copy and compare `<RDS_ENDPOINT>`. |
| RDS Access denied | Database password is wrong | Re-enter it without storing it in the report or shell history. |
| Communications link failure | RDS SG/VPC path or endpoint is wrong | Verify RDS status, port `3306`, EC2 SG reference, and endpoint. |
| Public website is unreachable | Port `8080` is closed or public IP changed | Check EC2 SG, service state, and current address. |
| `java -jar` command is split | A backslash/blank line broke the command | Keep `ExecStart` on one line. |
| Check performed too soon | Spring Boot is still starting | Wait and read `journalctl`. |
| HTTP 413 | The deployed JAR has a lower multipart transport limit than the request, or an upstream layer rejects it | Confirm the current 50 MB file/request settings, rebuild, replace the JAR, and restart. The 2.09 MB image is a regression case, not the configured limit. |
| Valid upload is rejected | The avatar exceeds 5 MiB, the post image exceeds 15 MiB, or its JPEG/PNG/GIF/WebP signature is invalid | Use the correct business limit and a genuine supported image; do not allow SVG or trust the filename extension alone. |
| S3 AccessDenied | IAM object ARN/action/prefix is wrong | Compare the role policy with the exact SDK request. |
| S3 image returns 403 | The 10-minute presigned URL expired or was generated for the wrong key | Refresh the page, verify the `s3:` marker and Region, and keep the bucket private. |
| S3 object becomes orphaned | Replace/delete path did not remove the old key | Review application lifecycle logic and database reference. |
| SES MessageRejected | The Sandbox sender or administrator recipient is not verified | Verify both email identities in `ap-southeast-1`. |
| SES failure rolls back sign-up | Mail call is inside the registration transaction | Send after commit or catch failure outside the transaction. |
| CloudWatch stream is missing | Agent, file path, IAM permission, or Region is wrong | Check local file, agent status/log, role, and `ap-southeast-1`. |
| Alarm has Insufficient data | Wrong instance/Region or not enough periods | Verify metric dimension and wait for data. |

## Configuration

| Public item | Allowed demo state |
|---|---|
| EC2 SSH | `<MY_PUBLIC_IP>/32` |
| EC2 application | TCP `8080` public temporarily |
| RDS MySQL | EC2 security group only |
| S3 | No public access |
| Secrets | Not in Git, logs, or screenshots |

## Verification

- Re-run `systemctl`, `journalctl`, `ss`, process, and HTTP checks.
- Review EC2 and RDS security groups side by side.
- Review S3 permissions and one object lifecycle result.
- Review SES identity/Sandbox state, console send, IAM Role CLI send, and one redacted administrator notification.
- Review CloudWatch Log Group Standard class, retention/event, and the `techblog-high-cpu` definition.
- Run a repository search for credential patterns before publishing the report.

## Expected Result

The deployment's public exposure, private data paths, AWS permissions, monitoring behavior, and known error fixes are documented and defendable.

## Cost and Security Notes

Close or restrict demo port `8080`, remove unnecessary test objects/logs, and stop chargeable resources after evidence collection. HTTPS and managed secret storage remain future improvements.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/final-security-review.png. Caption: *Figure 5.6.7.1. Redacted final EC2, RDS, S3, IAM, SES, and CloudWatch security review.* -->
