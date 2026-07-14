---
title: "Test S3 Upload and Troubleshoot HTTP 413"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## Objective

Verify the complete S3 image lifecycle for avatars and posts, document the real HTTP 413 incident, and distinguish Spring's transport limit from application-level image rules.

## Prerequisites

- TechBlog runs with `techblog-ec2-role`.
- `AWS_REGION`, `TECHBLOG_STORAGE_PROVIDER=s3`, `TECHBLOG_S3_BUCKET`, and `TECHBLOG_PRESIGN_TTL_MINUTES=10` are configured.
- S3 bucket is private and contains `avatars/` and `posts/`.
- A non-sensitive avatar PNG of approximately 2.09 MB and a post-image test file are available.

## Steps

### 1. Test avatar creation

1. Sign in as a USER/Reader.
2. Upload the test avatar.
3. Open S3 and confirm one new object appears under `avatars/`.
4. Refresh the profile and confirm the private image displays through the application's controlled-access method.
5. Inspect the matching RDS field and confirm it stores `s3:avatars/<uuid>.<ext>`, not binary data or a presigned URL.

### 2. Test avatar replacement and deletion

1. Replace the avatar with a second test image.
2. Confirm the profile shows the new image.
3. Confirm the database reference changes as expected.
4. Confirm the old object is deleted or retained only if the source code deliberately versions it.
5. If the UI supports avatar removal, remove it and verify the corresponding S3 behavior.

### 3. Test post-image lifecycle

1. Sign in as EDITOR/Writer and create a post with an image.
2. Confirm the object appears under `posts/`.
3. Confirm the post record stores `s3:posts/<uuid>.<ext>`.
4. Edit or delete the post image and verify the old object is handled according to the implementation.
5. Complete Admin approval to ensure the public post still renders the private image correctly.

## HTTP 413 Incident and Current Limits

An earlier deployed artifact returned HTTP 413 for a PNG avatar of approximately 2.09 MB. The current source now configures Spring's multipart transport layer as follows:

```properties
spring.servlet.multipart.enabled=true
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=50MB
```

These 50 MB values only determine whether Spring accepts the multipart request. `ImageFileValidator` applies stricter business rules after transport parsing: an avatar may be at most 5 MiB and a post image at most 15 MiB. It reads the file signature and accepts JPEG, PNG, GIF, or WebP; SVG and files that merely use an image filename extension are rejected. The 2.09 MB avatar is therefore a verified regression case, not the configured limit.

Rebuild and upload the new JAR, then back up and replace the running artifact:

```bash
cp /home/ec2-user/techblog.jar /home/ec2-user/techblog-backup.jar
mv /home/ec2-user/techblog-new.jar /home/ec2-user/techblog.jar
sudo systemctl restart techblog
curl -I http://localhost:8080
```

## Configuration

| Item | Value |
|---|---|
| Avatar prefix | `avatars/` |
| Post-image prefix | `posts/` |
| Test failure | HTTP 413 with a roughly 2.09 MB PNG |
| Multipart file limit | 50 MB |
| Multipart request limit | 50 MB |
| Avatar application limit | 5 MiB |
| Post-image application limit | 15 MiB |
| Accepted signatures | JPEG, PNG, GIF, WebP |
| Rejected examples | Empty file, SVG, disguised non-image, file over the relevant business limit |
| Bucket access | Private |
| Database content | `s3:avatars/...` or `s3:posts/...` marker only |
| Display access | Presigned GET URL, 10-minute lifetime |

## Verification

Capture one redacted before/after sequence for each upload type:

- UI action succeeds.
- Correct S3 prefix receives the object.
- Database reference matches the expected key.
- Private image displays through a 10-minute presigned GET URL; legacy local images still render.
- The verified replace/delete flow removes the corresponding managed S3 object and does not delete a legacy local reference.
- `curl` returns HTTP 200 after redeployment.

## Expected Result

Avatar and post-image operations use S3 correctly. The 2.09 MB avatar no longer returns HTTP 413, while oversized or unsupported images are still rejected by the relevant application rule.

## Troubleshooting

| Issue | Resolution |
|---|---|
| HTTP 413 persists | Confirm the active JAR contains the 50 MB settings and that no upstream layer has a smaller limit. |
| Request is accepted but validation fails | Compare the file with the 5 MiB avatar or 15 MiB post limit and verify its JPEG/PNG/GIF/WebP signature. |
| S3 AccessDenied | Check the instance role, bucket ARN, prefix, and API action. |
| Object exists but image is broken | Check controlled-access URL/response and database object reference. |
| Old object remains after replacement | Review delete behavior and add a cleanup action only if consistent with business rules. |
| Upload succeeds locally but not on EC2 | Compare runtime Region, bucket variable, role, and active JAR version. |

## Cost and Security Notes

Reject unsupported file types, validate size and signature, use generated object names, and remove test objects. Never enable SVG or make the bucket public to fix image rendering.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/http-413.png. Caption: *Figure 5.6.2.1. HTTP 413 before updating multipart limits.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/s3-avatars-objects.png. Caption: *Figure 5.6.2.2. Redacted avatar objects under avatars/.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/s3-posts-objects.png. Caption: *Figure 5.6.2.3. Redacted post-image objects under posts/.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/avatar-upload-success.png. Caption: *Figure 5.6.2.4. Avatar upload succeeds after the multipart fix.* -->
