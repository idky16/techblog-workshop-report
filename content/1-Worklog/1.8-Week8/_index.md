---
title: "Week 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

## Time

08/06/2026 - 14/06/2026

## Weekly Objectives

- Move new avatar and post-image operations to private Amazon S3.
- Preserve all legacy local image references and strengthen upload validation.

## Work Completed

- Integrated AWS SDK for Java v2 through a storage abstraction with local and S3 providers.
- Stored new references as `s3:avatars/<uuid>.<ext>` or `s3:posts/<uuid>.<ext>` and generated ten-minute presigned GET URLs only when rendering.
- Kept legacy `/images/avatars/...` and `posts/...` references compatible with the existing local storage folders.
- Added signature validation for JPEG, PNG, GIF, and WebP; rejected empty, SVG, disguised, and oversized files.
- Corrected the HTTP 413 regression by setting the Spring multipart transport limit to 50 MB while retaining 5 MiB avatar and 15 MiB post-image business limits.

## Results and Lessons

- Verified upload, display, replace, and delete behavior for both avatar and post-image objects.
- Confirmed that the bucket remains private with no public ACL, bucket policy, or browser CORS requirement.
- Learned to separate HTTP multipart parsing limits from stricter application-level validation.
