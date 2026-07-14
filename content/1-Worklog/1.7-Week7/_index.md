---
title: "Week 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

## Time

01/06/2026 - 07/06/2026

## Weekly Objectives

- Create the EC2 runtime and complete database migration.
- Keep TechBlog running independently of an interactive SSH session.

## Work Completed

- Launched `techblog-ec2` with Amazon Linux 2023, `t3.micro`, 8 GiB gp3, the prepared Security Group, and `techblog-ec2-role`.
- Restricted SSH to the current personal IP, opened TCP `8080` for the demo, and installed Java 17 Amazon Corretto plus the MariaDB client.
- Uploaded `techblog.sql`, connected to RDS, imported the schema, and verified the nine TechBlog tables.
- Built and uploaded `techblog-0.0.1-SNAPSHOT.jar`, configured `/etc/techblog/techblog.env`, and ran the application using `techblog.service`.

## Results and Lessons

- Verified `active (running)` and HTTP 200 from `curl -I http://localhost:8080`.
- Kept the web application running after the SSH session closed.
- Resolved deployment issues involving SSH allow-list changes, PEM permissions, an incorrect RDS endpoint, a wrong database password, and port `8080` access.
