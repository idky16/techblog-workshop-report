---
title: "Week 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

## Time

04/05/2026 - 10/05/2026

## Weekly Objectives

- Verify core TechBlog business flows and authorization boundaries locally.
- Convert functional requirements into an AWS deployment checklist.

## Work Completed

- Tested Guest and USER/Reader flows: registration, sign-in, reading, like, save, comment, reply, report, profile, avatar, and Writer request.
- Tested EDITOR/Writer ownership rules and the `PENDING`, `APPROVED`, and `REJECTED` post lifecycle.
- Reviewed ADMIN flows for users, categories, comments, reports, Writer requests, and post approvals.
- Ran the Maven Wrapper build and recorded required environment values without copying credentials into the report.

## Results and Lessons

- Created a functional test matrix for Guest, USER, EDITOR, and ADMIN.
- Confirmed that AWS migration must preserve authentication, authorization, database behavior, and server-rendered Thymeleaf pages.
- Identified RDS, private object storage, process management, monitoring, and cost controls as the main infrastructure requirements.
