---
title: "Week 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

## Time

18/05/2026 - 24/05/2026

## Weekly Objectives

- Establish cost control and the shared foundation services before compute deployment.
- Prepare private media storage and role-based AWS access without static keys.

## Work Completed

- Created AWS Budget monitoring before provisioning the main chargeable resources.
- Created private bucket `techblog-uploads-ttv-2026` with Block Public Access, SSE-S3, and prefixes `avatars/` and `posts/`.
- Created `techblog-ec2-role` and prepared `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy`, and inline `TechBlogSesSendEmail` for the verified demo state.
- Verified an Amazon SES email identity in `ap-southeast-1` and recorded the Sandbox restriction.

## Results and Lessons

- Confirmed that no Access Key or Secret Access Key is required in source code.
- Established the private S3 and IAM boundary required by the application.
- Documented that `AmazonS3FullAccess` is the current demo policy and should be narrowed to bucket/prefix least privilege later.
