---
title: "Week 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

## Time

15/06/2026 - 21/06/2026

## Weekly Objectives

- Integrate a small transactional-email use case without changing registration behavior.
- Respect Amazon SES Sandbox and IAM Role security boundaries.

## Work Completed

- Added AWS SDK v2 SES support using `SesV2Client`, the default credential chain, and `techblog-ec2-role`.
- Configured a verified sender and verified administrator recipient through environment variables.
- Sent an administrator notification only after a new user was stored successfully; included username, display name, registration email, and time.
- Implemented best-effort handling so an Amazon SES failure logs a short warning but does not roll back registration.
- Tested sending through the console, the EC2 IAM Role with AWS CLI, and the Spring Boot registration flow.

## Results and Lessons

- Verified the administrator registration notification in the SES Sandbox.
- Confirmed that no password, password hash, JWT, cookie, or AWS credential appears in the email or log.
- Documented that Sandbox sending is limited to verified recipients and is not a general-purpose welcome-email workflow.
