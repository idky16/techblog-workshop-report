---
title: "Deploy Spring Boot to EC2"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
alwaysopen: true
---

## Module Objective

Create Amazon EC2, attach the prepared IAM Role/security group, import the local database into RDS from EC2, deploy the TechBlog JAR, configure runtime integrations, and keep the application running through `systemd`.

## Module Flow

{{< mermaid align="center" >}}
flowchart LR
    D[Local techblog.sql] -->|scp and mysql import| E[Amazon EC2<br/>techblog-ec2]
    E --> R[(Amazon RDS for MySQL)]
    L[Local Windows<br/>Maven Wrapper] -->|build and scp| J[techblog.jar]
    J --> E
    C[Restricted environment file<br/>RDS + S3 + SES + logging] --> E
    E -->|systemd| S[techblog.service]
    S -->|HTTP :8080| W[TechBlog website]
{{< /mermaid >}}

## Child Pages

1. [Create EC2 and Configure Security Group](5.5.1-create-ec2/)
2. [SSH and Install Java 17](5.5.2-ssh-java/)
3. [Connect to RDS and Import the Database](5.5.3-rds-import/)
4. [Build and Upload the Spring Boot JAR](5.5.4-build-upload-jar/)
5. [Configure Runtime Integration for RDS, S3, and SES](5.5.5-runtime-integration/)
6. [Run TechBlog with systemd](5.5.6-systemd/)

## Module Result

| Item | Expected result |
|---|---|
| EC2 | `techblog-ec2` runs Amazon Linux 2023 with `techblog-ec2-role`. |
| Java | Java 17 Amazon Corretto is installed. |
| Database migration | EC2 connects to private RDS and imports the nine TechBlog tables. |
| JAR | `techblog-0.0.1-SNAPSHOT.jar` is uploaded as `/home/ec2-user/techblog.jar`. |
| Runtime | RDS, S3, SES, Region, and log-file values are supplied without static AWS keys. |
| Service | `techblog.service` is `active (running)`. |
| Website | Local and public port-`8080` checks return HTTP 200. |

## Cost and Security Notes

The demo uses one `t3.micro` instance and 8 GiB gp3. SSH remains restricted to `<MY_PUBLIC_IP>`; port `8080` is public only for the demo. The environment file is mode `600` and is never included in screenshots.
