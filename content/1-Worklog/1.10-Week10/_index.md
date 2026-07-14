---
title: "Week 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

## Time

22/06/2026 - 28/06/2026

## Weekly Objectives

- Centralize appropriate application logs and create a simple EC2 health signal.
- Keep monitoring useful without collecting sensitive data or adding unnecessary services.

## Work Completed

- Configured Spring Boot to write application events to `/var/log/techblog/application.log`.
- Installed and configured CloudWatch Agent to publish to Standard-class Log Group `/techblog/application` with 14-day retention.
- Verified the log stream named by the EC2 instance ID and checked new events after website requests.
- Created `techblog-high-cpu` for Average `CPUUtilization > 70%`, five-minute periods, 2 of 2 datapoints, with missing data treated as not breaching.

## Results and Lessons

- Centralized INFO, WARN, and ERROR application events in CloudWatch Logs.
- Confirmed that passwords, tokens, cookies, and sensitive request data must not be logged.
- Recorded that the CPU alarm has no notification action and is reviewed directly in the CloudWatch console.
