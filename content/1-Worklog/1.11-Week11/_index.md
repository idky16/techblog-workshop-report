---
title: "Week 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

## Time

29/06/2026 - 05/07/2026

## Weekly Objectives

- Perform end-to-end testing across application roles and AWS integrations.
- Review security boundaries and turn deployment failures into troubleshooting guidance.

## Work Completed

- Re-tested Guest, USER, EDITOR, and ADMIN scenarios against the deployed RDS database.
- Verified S3 avatar/post-image create, display, replace, and delete behavior plus local legacy-image compatibility.
- Checked Amazon SES registration notification, CloudWatch log events, `techblog-high-cpu`, `systemd`, port `8080`, and the Java process.
- Reviewed SSH, EC2 and RDS Security Groups, S3 Block Public Access, IAM Role credentials, protected environment files, and SES Sandbox limits.
- Consolidated known issues: changing public IP, PEM permissions, incorrect endpoint/password, connection failure, broken Java command, closed port `8080`, slow restart, and HTTP 413.

## Results and Lessons

- Completed a practical regression checklist for the live demo.
- Confirmed the current security boundary and documented limitations such as HTTP, a changeable public IP, Single-AZ, and console-only CPU alarms.
- Learned to verify service, port, process, database, object, email, and log layers separately during troubleshooting.
