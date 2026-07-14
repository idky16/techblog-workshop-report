---
title: "Configure an Amazon SES Verified Email Identity"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

## Objective

Configure the verified email identity used to send a best-effort operational notice to the TechBlog administrator after a new user registration is saved successfully.

## Prerequisites

- Region is `ap-southeast-1`.
- The group controls the sender mailbox represented by `<SES_VERIFIED_SENDER_EMAIL>`.
- The configured administrator recipient, represented by `<SES_VERIFIED_ADMIN_EMAIL>`, can also be verified because the account remains in Sandbox.

## Steps

1. Open **Amazon SES** and select `ap-southeast-1`.
2. Open **Configuration → Identities → Create identity**.
3. Select **Email address**. The final demo does not use a domain identity.
4. Enter the sender address; use `<SES_VERIFIED_SENDER_EMAIL>` in the report.
5. Open the verification message and complete the verification link.
6. Return to SES and confirm status **Verified**.
7. Confirm the administrator recipient is also a verified address while the account is in Sandbox.
8. Open **Account dashboard** and record the Sandbox state without exposing addresses or account details.

## Configuration

| Setting | Verified value |
|---|---|
| Region | `ap-southeast-1` |
| Identity type | Email address |
| Sender placeholder | `<SES_VERIFIED_SENDER_EMAIL>` |
| Administrator placeholder | `<SES_VERIFIED_ADMIN_EMAIL>` |
| Account state | Sandbox |
| Application purpose | New-user registration notice to administrator |
| SMTP credentials | Not used |
| Domain identity | Not used |

The email contains only username, display name when available, registration email, and registration time. It must never contain a password, password hash, JWT, cookie, AWS credential, or database secret.

## Sandbox Rule

SES Sandbox permits sending only from verified identities and to verified recipients. TechBlog therefore sends the operational notice to the configured verified administrator recipient, not to the arbitrary email entered by the registering user. The group has not requested production access.

## Verification

- The email identity is **Verified** in `ap-southeast-1`.
- Copy the verified identity ARN only into the redacted policy workflow; keep the report value as `<SES_VERIFIED_IDENTITY_ARN>`.
- Complete or review `TechBlogSesSendEmail` so it contains only `ses:SendEmail` and `ses:SendRawEmail` for this identity.
- Do not run an EC2 CLI test here. Role attachment and runtime tests occur after EC2 is created in modules 5.5 and 5.6.

## Expected Result

The sender and administrator recipient satisfy the Sandbox boundary. The identity-scoped policy is ready, while IAM Role CLI and end-to-end application tests remain in 5.6.3 after EC2 exists.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Verification message is missing | Check spam, confirm the address, and resend verification. |
| Identity is verified in another Region | Switch to `ap-southeast-1` and verify it there. |
| `MessageRejected` in Sandbox | Confirm both sender and administrator recipient are verified. |

## Cost and Security Notes

Use a small number of plain-text test messages. Do not use SMTP credentials, attachments, Dedicated IP, Mail Manager, Global Endpoint, or a domain identity.

<!-- TODO: Add /images/5-Workshop/5.3-Budget-S3-IAM/ses-verified-identity.png. Caption: *Figure 5.3.4.1. Redacted verified Amazon SES email identity and Sandbox state in ap-southeast-1.* -->
