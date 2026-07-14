---
title: "Week 6"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Time

25/05/2026 - 31/05/2026

## Weekly Objectives

- Replace local MySQL with a private managed database.
- Prepare migration data and the EC2-to-RDS security boundary in the correct order.

## Work Completed

- Created `techblog-db` using Amazon RDS for MySQL, `db.t3.micro`, Single-AZ, database `techblog`, and public access disabled.
- Prepared the EC2 Security Group and configured the RDS Security Group to accept MySQL `3306` only from that group.
- Recorded endpoint, database, port, and username using safe placeholders in the report.
- Exported the local database to `techblog.sql` with `mysqldump` and checked the dump before EC2 creation.

## Results and Lessons

- Built a private database path without opening MySQL to `0.0.0.0/0`.
- Prepared a repeatable migration file while keeping passwords and the complete endpoint out of documentation.
- Understood why network testing and import must wait until EC2 exists.
