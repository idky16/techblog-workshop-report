---
title: "Integrate S3, IAM, CloudFront, and WAF"
date: 2026-07-10
weight: 5
pre: " <b> 5.5. </b> "
---

Create one private, encrypted S3 bucket with `avatars/` and `posts/` prefixes. Grant the EC2 role only the required object operations on this bucket. Validate MIME type, extension, file size, and generated object keys before upload.

Test avatar and post-image uploads, confirm the objects remain available after EC2 restarts, and ensure uploaded files no longer depend on instance storage.

Once the EC2 endpoint is stable, create a CloudFront distribution with EC2 as its origin. Forward the methods, headers, cookies, and query strings required by Spring MVC; do not cache login, administration, or personalized responses. Attach an AWS WAF Web ACL, begin with managed rules, and consider rate-based protection for login, registration, and comments.

The demo uses the CloudFront endpoint and does not require Route 53 or a custom domain.
