---
title: "Prerequisites"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Objective

Prepare the local TechBlog baseline, AWS account, safe access method, and deployment values before creating resources. Completing this page prevents the group from debugging application code, database migration, and AWS permissions at the same time.

## AWS Account and Region

- Use one shared AWS account for the TTV demo.
- Select **Asia Pacific (Singapore) – `ap-southeast-1`** for EC2, RDS, S3, SES, and CloudWatch.
- Do not share the root account. Reserve root for billing or account recovery.
- Use an IAM identity with only the permissions required for the assigned task.
- Create AWS Budgets before EC2, RDS, or other chargeable resources.
- Confirm the verified Amazon SES email identity and the account's Sandbox status in `ap-southeast-1`.

## Local Tools

The Windows development machine needs Java 17, the Maven Wrapper included in the TechBlog repository, Git/GitHub access, MySQL/Laragon, OpenSSH, and a browser.

```powershell
java -version
.mvnw.cmd --version
git --version
ssh -V
```

Expected checks:

| Tool | Expected result |
|---|---|
| Java | Version 17 |
| Maven | Wrapper runs without a global Maven installation |
| Git | Repository can be read and updated |
| MySQL/Laragon | Local database `techblog` is available |
| OpenSSH | `ssh` and `scp` commands are available |

## Check the Project and AWS SDK Configuration

Confirm that TechBlog remains one Spring Boot MVC project and that the build produces:

```text
target/techblog-0.0.1-SNAPSHOT.jar
```

Review `pom.xml` and application configuration for the AWS SDK for Java modules used by S3 and SES. Do not invent dependency versions in the report; record the exact version found in the repository. Verify that the code obtains credentials from the default AWS credential provider chain so EC2 can use its IAM Role.

The application configuration must support:

- Region `ap-southeast-1`.
- Storage provider value `s3` on EC2; local remains the development default.
- S3 bucket `techblog-uploads-ttv-2026`.
- S3 presigned GET lifetime of 10 minutes.
- SES sender `<SES_VERIFIED_SENDER_EMAIL>`.
- SES administrator recipient `<SES_VERIFIED_ADMIN_EMAIL>` and enabled flag.
- RDS URL, username, and password through environment values.
- Application log file `/var/log/techblog/application.log`.
- No hard-coded Access Key, Secret Access Key, RDS password, endpoint, or personal email.

## Check Local Database and Upload Baseline

Before migration, confirm local MySQL/Laragon has database `techblog` and the expected tables. Test the original local upload flows so later S3 verification has a baseline:

- Avatar upload and replacement.
- Post-image upload and replacement.
- Database field/mapping used for the local-path baseline and the S3 object key/reference.
- Delete behavior when a user or post image changes.

Do not remove local folders until the S3 path has passed create/read/replace/delete tests.

## Build Locally

Build the application from the project root:

```powershell
.mvnw.cmd clean package -DskipTests
```

Then verify the generated artifact:

```powershell
Get-Item .\target\techblog-0.0.1-SNAPSHOT.jar
```

## Test Locally

Run TechBlog at `http://localhost:8080` and record the result of:

- Sign up, sign in, and sign out.
- USER/Reader: read, like, save, comment, reply, report, profile, avatar, Writer request.
- EDITOR/Writer: create, edit, delete owned posts and view moderation status.
- ADMIN: approve/reject posts and Writer requests; manage users, categories, comments, and reports.
- Verify the current upload boundaries from source: 50 MB per multipart file/request, 5 MiB per avatar, and 15 MiB per post image. Test valid JPEG/PNG/GIF/WebP signatures and confirm SVG or disguised non-image files are rejected.

## Prepare AWS Deployment Inputs

Use placeholders in the report and keep real values outside Git:

| Input | Safe report value |
|---|---|
| EC2 public address | `<EC2_PUBLIC_IP>` |
| RDS endpoint | `<RDS_ENDPOINT>` |
| Database password | `<DB_PASSWORD>` |
| Personal public IP | `<MY_PUBLIC_IP>` |
| SES sender | `<SES_VERIFIED_SENDER_EMAIL>` |
| SES administrator recipient | `<SES_VERIFIED_ADMIN_EMAIL>` |
| Region | `ap-southeast-1` |
| S3 bucket | `techblog-uploads-ttv-2026` |

CloudWatch Agent is installed on EC2 later; no agent is required on the local machine.

## Prepare the Key Pair and Security Rules

Store the PEM file outside the repository, for example `C:\WORKING\techblog-key.pem`. Keep PEM contents out of Markdown and screenshots. Plan the initial inbound rules:

- SSH `22` from `<MY_PUBLIC_IP>` only.
- TCP `8080` public only for the demo window.
- RDS `3306` from the EC2 security group only.

No static AWS credentials are required because `techblog-ec2-role` is attached to EC2.

## Pre-deployment Checklist

- [ ] The local website works at `http://localhost:8080`.
- [ ] Guest, USER, EDITOR, and ADMIN scenarios pass locally.
- [ ] Java 17 and Maven Wrapper commands work.
- [ ] `target/techblog-0.0.1-SNAPSHOT.jar` is generated.
- [ ] Local database `techblog` is ready for `mysqldump`.
- [ ] S3/SES AWS SDK configuration exists and contains no static credential.
- [ ] Region is `ap-southeast-1`.
- [ ] AWS Budgets will be created first.
- [ ] The PEM file is outside Git.
- [ ] SES Sandbox state, verified sender, and verified administrator recipient are confirmed.
- [ ] Screenshot redaction rules are understood.

## Expected Result

The group has a repeatable local baseline, a successful JAR build, safe placeholders, and the account prerequisites required to start module 5.3.

## Troubleshooting

| Issue | Resolution |
|---|---|
| `java` is not version 17 | Select the Java 17 JDK in `JAVA_HOME` and IntelliJ. |
| Maven Wrapper fails | Confirm `.mvn/`, `mvnw`, and `mvnw.cmd` exist and network access is available for dependencies. |
| Local role test fails | Fix the application baseline before provisioning AWS resources. |
| Static AWS key found in configuration | Remove it and use the EC2 IAM Role/default provider chain. |
| SES identity is in a different Region | Create/verify the identity in `ap-southeast-1`. |

## Cost and Security Notes

Preparation itself should not require chargeable compute. Verify the Region before resource creation, avoid root usage, and create the Budget guardrail first.

<!-- TODO: Add /images/5-Workshop/5.2-Prerequisites/local-build-success.png. Caption: *Figure 5.2.1. Successful local Maven Wrapper build.* -->
<!-- TODO: Add /images/5-Workshop/5.2-Prerequisites/region-selection.png. Caption: *Figure 5.2.2. Asia Pacific (Singapore) selected in the AWS Console.* -->
