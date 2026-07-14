---
title: "Create and Secure the S3 Bucket"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## Objective

Create the private S3 bucket used by TechBlog for avatar and post-image objects. The bucket is application storage, not static website hosting.

## Prerequisites

- Region: `ap-southeast-1`.
- Bucket name: `techblog-uploads-ttv-2026`.
- The application has AWS SDK for Java S3 integration and will run with an EC2 IAM Role.

## Steps

1. Open **Amazon S3** and choose **Create bucket**.
2. Enter `techblog-uploads-ttv-2026`.
3. Select **Asia Pacific (Singapore) – ap-southeast-1**.
4. Keep **Object Ownership: ACLs disabled**.
5. Keep all **Block Public Access** options enabled.
6. Enable default server-side encryption with **Amazon S3 managed keys (SSE-S3)**.
7. Keep Bucket Versioning disabled for the cost-conscious demo.
8. Do not enable Transfer Acceleration or static website hosting.
9. Create two prefixes by using **Create folder**:
   - `avatars/`
   - `posts/`
10. Review the **Permissions** and **Properties** tabs before continuing.

## Configuration

| Setting | Value |
|---|---|
| Bucket | `techblog-uploads-ttv-2026` |
| Region | `ap-southeast-1` |
| Object Ownership | ACLs disabled |
| Public access | Blocked |
| Default encryption | SSE-S3 |
| Versioning | Disabled for demo |
| Prefixes | `avatars/`, `posts/` |
| Website hosting | Disabled |

## Application Storage Convention

Spring Boot uses AWS SDK operations to upload, read, replace, and delete objects through `techblog-ec2-role`. The implemented application stores database markers in this format:

```text
s3:avatars/<uuid>.<ext>
s3:posts/<uuid>.<ext>
```

The `s3:` prefix is an application marker and is not part of the S3 object key. The object keys are `avatars/<uuid>.<ext>` and `posts/<uuid>.<ext>`. MySQL stores the marker, not the image binary or a presigned URL. Because the bucket is private, the application creates a presigned GET URL with a 10-minute lifetime during rendering. Legacy local image references continue to use the existing local path.

## Verification

From the S3 console, verify:

- Block Public Access shows **On**.
- Default encryption shows **SSE-S3**.
- `avatars/` and `posts/` exist.
- No bucket policy or object ACL grants public access.

Application-level object verification is performed in 5.6.2 after the EC2 role and runtime values are configured.

## Expected Result

The private bucket is integrated with TechBlog. New avatar and post-image create, replace, and delete operations use the correct prefixes without public exposure.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Bucket name already exists | Confirm the group's globally unique name; do not silently document a different bucket. |
| AccessDenied later from EC2 | Check the role policy resource and exact prefix. |
| Image URL returns 403 | Verify the application uses the intended controlled-access method; do not make the bucket public. |
| Object appears in the wrong prefix | Review key construction in the avatar/post storage service. |

## Cost and Security Notes

Keep test files small, lifecycle test objects temporary, and Transfer Acceleration disabled. Block Public Access must remain enabled even when debugging image display.

<!-- TODO: Add /images/5-Workshop/5.3-Budget-S3-IAM/s3-bucket-settings.png. Caption: *Figure 5.3.2.1. Private TechBlog bucket with Block Public Access and SSE-S3.* -->
<!-- TODO: Add /images/5-Workshop/5.3-Budget-S3-IAM/s3-prefixes.png. Caption: *Figure 5.3.2.2. The avatars/ and posts/ prefixes.* -->
