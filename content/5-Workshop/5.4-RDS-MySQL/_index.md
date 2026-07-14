---
title: "Prepare RDS MySQL"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
alwaysopen: true
---

## Module Objective

This module creates private Amazon RDS for MySQL and prepares everything needed before EC2 exists: the security-group boundary, safe connection placeholders, and the local SQL dump. Connection and import commands are deliberately deferred to 5.5.3.

## Module Flow

{{< mermaid align="center" >}}
flowchart LR
    L[Local MySQL<br/>techblog] -->|mysqldump| F[techblog.sql]
    E[EC2 security group<br/>prepared only] -->|source reference :3306| D[RDS security group]
    D --> R[(Amazon RDS for MySQL<br/>techblog-db)]
    F -. upload and import in 5.5.3 .-> R
{{< /mermaid >}}

## Child Pages

1. [Create RDS MySQL](5.4.1-create-rds/)
2. [Configure EC2-RDS Security Groups](5.4.2-security-groups/)
3. [Record RDS Connection Settings](5.4.3-connection-settings/)
4. [Export the Local TechBlog Database](5.4.4-export-database/)

## Module Result

| Item | Expected result |
|---|---|
| RDS | `techblog-db` is available. |
| Security | The RDS inbound rule references the prepared EC2 security group on port `3306`. |
| Connection values | `<RDS_ENDPOINT>`, database `techblog`, port `3306`, and username are recorded without exposing secrets. |
| Local export | `techblog.sql` exists locally and is ready for transfer after EC2 is created. |

No SSH, `mysql`, `scp`, or RDS import command runs in this module. Image binaries and presigned URLs are not stored in MySQL; after deployment, new media references use `s3:avatars/<uuid>.<ext>` or `s3:posts/<uuid>.<ext>` and legacy local references remain unchanged.
