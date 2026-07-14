---
title: "Connect to RDS and Import the Database"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

## Objective

After EC2 exists, verify the private EC2-to-RDS path and import the local `techblog.sql` dump into database `techblog`.

## Prerequisites

- `techblog-ec2` is running with the application security group prepared in 5.4.2.
- RDS is **Available** and port `3306` accepts that EC2 security group.
- MariaDB client is installed on EC2.
- `C:\WORKING\techblog.sql` was exported in 5.4.4.

## Steps

### 1. Upload the SQL dump to EC2

Run from local PowerShell:

```powershell
scp -i "C:\WORKING\techblog-key.pem" "C:\WORKING\techblog.sql" ec2-user@<EC2_PUBLIC_IP>:/home/ec2-user/
```

### 2. Verify RDS connectivity from EC2

```bash
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p
```

Then run:

```sql
SHOW DATABASES;
USE techblog;
SHOW TABLES;
```

Exit the client before the import if necessary.

### 3. Import and verify the database

```bash
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p techblog < /home/ec2-user/techblog.sql
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p techblog
```

```sql
SHOW TABLES;
```

The verified schema contains these nine tables:

`categories`, `comment_reports`, `comments`, `editor_upgrade_requests`, `post_likes`, `posts`, `saved_collections`, `saved_posts`, `users`.

After verification, remove the dump from EC2 if it is no longer needed:

```bash
rm /home/ec2-user/techblog.sql
```

## Configuration

| Item | Value |
|---|---|
| Client host | `techblog-ec2` |
| Database host | `<RDS_ENDPOINT>` |
| Port | `3306` |
| Database | `techblog` |
| Import file | `/home/ec2-user/techblog.sql` |

## Verification

Confirm the client connects through the security-group reference and `SHOW TABLES` returns the nine tables. Do not expose the endpoint, password, row data, or personal information in screenshots.

## Expected Result

The private RDS database contains the migrated TechBlog schema/data and is ready for the Spring Boot runtime configuration in 5.5.5.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Unknown MySQL server host | Copy `<RDS_ENDPOINT>` again and check every character. |
| Access denied | Re-enter the correct password without writing it into the report or command history. |
| Connection timeout | Confirm RDS is available and its inbound source is the EC2 security group. |
| Import fails | Confirm database `techblog` exists and the dump path is correct. |

## Cost and Security Notes

Do not open RDS to `0.0.0.0/0`. Remove the SQL dump from EC2 after successful verification when it is not required for recovery.

<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/rds-import-verification.png. Caption: *Figure 5.5.3.1. Redacted EC2-to-RDS connection and nine-table import verification.* -->
