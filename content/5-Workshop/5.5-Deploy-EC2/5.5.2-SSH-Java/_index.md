---
title: "SSH and Install Java 17"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Objective

Connect to EC2 from Windows and install Java 17 Amazon Corretto plus the MariaDB client required for the RDS import.

## Prerequisites

- PEM key exists at `C:\WORKING\techblog-key.pem`.
- EC2 public IP is known as `<EC2_PUBLIC_IP>`.

## Steps

Fix Windows PEM permissions:

```powershell
icacls "C:\WORKING\techblog-key.pem" /inheritance:r
icacls "C:\WORKING\techblog-key.pem" /remove "BUILTIN\Users"
icacls "C:\WORKING\techblog-key.pem" /grant:r "$($env:USERNAME):(R)"
```

Connect through SSH:

```powershell
ssh -i "C:\WORKING\techblog-key.pem" ec2-user@<EC2_PUBLIC_IP>
```

Install Java 17:

```bash
sudo dnf update -y
sudo dnf install -y java-17-amazon-corretto-headless
sudo dnf install -y mariadb105
java -version
```

## Configuration

| Item | Value |
|---|---|
| User | `ec2-user` |
| Key | `C:\WORKING\techblog-key.pem` |
| Java | Amazon Corretto 17 |
| Database client | MariaDB client compatible with RDS MySQL |

## Verification

`java -version` should show Java 17, and `mysql --version` should show the installed client.

## Expected Result

EC2 can run the Spring Boot JAR.

## Common Issues

| Issue | Fix |
|---|---|
| Bad permissions on PEM | Run the `icacls` commands. |
| SSH timeout | Update the SSH source IP to current `<MY_PUBLIC_IP>`. |

## Cost and Security Notes

Do not upload the PEM file to Git or include it in screenshots.

<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/java-17.png. Caption: *Figure 5.5.2.1. Java 17 Amazon Corretto installed on EC2.* -->
