---
title: "Build and Upload the Spring Boot JAR"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

## Objective

Build the TechBlog executable JAR locally and upload it to EC2.

## Prerequisites

- Local build tools are available.
- EC2 SSH works.

## Steps

Build on Windows:

```powershell
.\mvnw.cmd clean package -DskipTests
```

Upload the JAR:

```powershell
scp -i "C:\WORKING\techblog-key.pem" "C:\WORKING\techblog\target\techblog-0.0.1-SNAPSHOT.jar" ec2-user@<EC2_PUBLIC_IP>:/home/ec2-user/techblog.jar
```

Check file size on EC2:

```bash
ls -lh /home/ec2-user/techblog.jar
```

## Configuration

| Item | Value |
|---|---|
| Build command | `.\mvnw.cmd clean package -DskipTests` |
| Remote path | `/home/ec2-user/techblog.jar` |
| JAR size | About 58 MB |

## Verification

The JAR exists on EC2 and has a reasonable file size.

## Expected Result

The executable JAR is ready for `systemd`.

## Common Issues

| Issue | Fix |
|---|---|
| Upload fails | Check PEM permissions and current EC2 public IP. |
| Wrong JAR path | Check the `target` folder after build. |

## Cost and Security Notes

Do not put passwords in upload commands or report screenshots.

<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/jar-upload.png. Caption: *Figure 5.5.4.1. TechBlog executable JAR uploaded to EC2.* -->
