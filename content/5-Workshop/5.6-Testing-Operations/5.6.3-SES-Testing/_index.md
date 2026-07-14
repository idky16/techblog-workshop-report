---
title: "Test Amazon SES and Registration Notification"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

## Objective

Verify Amazon SES at three boundaries: the SES console, AWS CLI on EC2 through `techblog-ec2-role`, and the Spring Boot registration flow. The application sends the final email to the configured administrator rather than to the registering address.

## Prerequisites

- The SES email identity is **Verified** in `ap-southeast-1`.
- The account remains in Sandbox; sender and administrator recipient are verified.
- `techblog-ec2-role` has inline policy `TechBlogSesSendEmail`.
- `TECHBLOG_SES_ENABLED=true`, `TECHBLOG_SES_FROM_EMAIL=<SES_VERIFIED_SENDER_EMAIL>`, and `TECHBLOG_SES_ADMIN_RECIPIENT=<SES_VERIFIED_ADMIN_EMAIL>` are configured.
- No SMTP credential, Access Key, or Secret Access Key is configured.

## Steps

### 1. Test from the Amazon SES console

1. Open **Amazon SES → Configuration → Identities** in `ap-southeast-1`.
2. Select the verified email identity and choose **Send test email**.
3. Use the verified administrator recipient because the account is in Sandbox.
4. Send a plain-text message with non-sensitive test content.
5. Confirm delivery in the recipient mailbox without recording the real address or message identifier.

### 2. Test from EC2 through the IAM Role

From `techblog-ec2`, confirm the caller uses the instance role, then perform a small SES v2 send using placeholders:

```bash
aws sts get-caller-identity

aws sesv2 send-email \
  --from-email-address "<SES_VERIFIED_SENDER_EMAIL>" \
  --destination "ToAddresses=<SES_VERIFIED_ADMIN_EMAIL>" \
  --content 'Simple={Subject={Data=TechBlog SES IAM Role Test},Body={Text={Data=SES call from techblog-ec2 via IAM Role}}}' \
  --region ap-southeast-1
```

Exclude the returned message ID, account ID, and real email address from the report. This verified test proves EC2 can call SES without a static key.

### 3. Test the end-to-end registration notification

1. Open the TechBlog sign-up page and register one non-sensitive test user.
2. Confirm registration succeeds and the user can sign in.
3. Confirm the configured administrator recipient receives subject **TechBlog - New User Registration**.
4. Verify the plain-text body contains only username, display name when available, registration email, and registration time.
5. Confirm the registering address is not used as the SES recipient.
6. Review application logs for a concise success/failure event without full email content or identifiers.
7. If a controlled SES failure occurs, confirm the saved user still exists and can sign in.

## Configuration

| Item | Verified value |
|---|---|
| Region | `ap-southeast-1` |
| Account state | Sandbox |
| Sender | `<SES_VERIFIED_SENDER_EMAIL>` |
| Recipient | `<SES_VERIFIED_ADMIN_EMAIL>` |
| Application subject | `TechBlog - New User Registration` |
| Credential source | `techblog-ec2-role` default credential chain |
| Failure behavior | Registration remains successful; a sanitized WARN is logged |

## Verification

- SES console test reaches the verified administrator recipient.
- AWS CLI send succeeds on EC2 through the IAM Role.
- A successfully saved registration triggers exactly one administrator notification.
- The message does not contain password, password hash, JWT, cookie, credential, or database secret.
- SES failure does not return an error page or roll back the user record.

## Expected Result

The verified administrator receives the operational registration notice when SES succeeds, while registration remains independent from temporary SES failures.

## Troubleshooting

| Issue | Resolution |
|---|---|
| `MessageRejected` | Confirm Sandbox state and verify both sender and administrator recipient in `ap-southeast-1`. |
| `AccessDeniedException` | Check `TechBlogSesSendEmail`, verified identity resource, and the role attached to EC2. |
| CLI uses another identity | Run `aws sts get-caller-identity` and remove unintended static/profile credentials. |
| Console/CLI succeeds but the application does not send | Check the three `TECHBLOG_SES_*` variables and restart `techblog.service`. |
| Registration fails when SES fails | Keep notification best-effort and catch the send exception at the notification boundary. |

## Cost and Security Notes

Send only a few plain-text test messages. Do not use attachments, bulk sending, SMTP credentials, Dedicated IP, Mail Manager, Global Endpoint, a domain identity, or production-access claims.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/ses-console-test.png. Caption: *Figure 5.6.3.1. Redacted successful SES console test to a verified Sandbox recipient.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/ses-cli-role-test.png. Caption: *Figure 5.6.3.2. Redacted SES CLI test from EC2 through techblog-ec2-role.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/ses-admin-registration-notice.png. Caption: *Figure 5.6.3.3. Redacted administrator notification after a successful TechBlog registration.* -->
