---
title: "Functional and Role-Based Testing"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Objective

Verify that the deployed Spring Boot MVC application preserves the expected public, USER, EDITOR, and ADMIN behavior while using RDS.

## Prerequisites

- `techblog.service` is `active (running)`.
- The website is reachable at `http://<EC2_PUBLIC_IP>:8080/`.
- RDS contains the migrated schema and test data.
- Separate non-sensitive test accounts are available for each role.

## Steps

1. Open the public URL in a private browser window and test Guest pages.
2. Register a new USER and confirm the account is stored before the best-effort administrator notification is attempted.
3. Sign in as USER and complete Reader actions.
4. Submit a Writer upgrade request.
5. Approve the request as ADMIN, then sign in as EDITOR.
6. Create a post, verify `PENDING`, and test owned-post edit/delete authorization.
7. Approve or reject the post as ADMIN and confirm the visible state.
8. Test comment/report moderation and category/tag management exposed by the current Admin UI.
9. Sign out after each role and verify unauthorized routes are rejected.

## Test Matrix

| Role | Scenario | Expected result |
|---|---|---|
| Guest | Home, post list/detail | Public pages render without authentication |
| Guest | Protected profile/editor/admin URL | Redirected to sign in or denied |
| USER / Reader | Sign up, sign in, sign out | Session starts and ends correctly |
| USER / Reader | Like and save/bookmark | State persists in RDS |
| USER / Reader | Comment, reply, report | Allowed data is stored and report reaches Admin workflow |
| USER / Reader | Profile and Writer request | Profile updates and request becomes reviewable |
| EDITOR / Writer | Create post | New post enters `PENDING` |
| EDITOR / Writer | Edit/delete owned post | Allowed only for the owner |
| EDITOR / Writer | Modify another Writer's post | Denied |
| ADMIN | Approve/reject post | Status changes to `APPROVED` or `REJECTED` |
| ADMIN | Approve/reject Writer request | User role changes according to the decision |
| ADMIN | Manage users/categories/tags/comments/reports | Authorized administration action succeeds |

## Configuration

Use test-only accounts and content. Do not include real emails, profile data, session cookies, CSRF values, or password fields in screenshots.

## Verification

- Refresh the browser after each data-changing action.
- Query or inspect the relevant RDS record without exposing user details.
- Confirm unauthorized requests do not silently succeed.
- Record pass/fail and evidence path for every row.

## Expected Result

All role boundaries and main blog functions behave consistently on the EC2/RDS deployment.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Login fails after migration | Check imported user records, password encoding, RDS connectivity, and application logs. |
| Role menu appears but action is denied | Compare the stored role and Spring Security rule. |
| Post stays `PENDING` | Complete the Admin approval flow and refresh the record. |
| Public website is unreachable | Check `systemd`, port `8080`, EC2 SG, and current public IP. |

## Cost and Security Notes

Use a small test dataset and delete it when the report no longer needs it. Never use a production-like personal account for screenshots.

## Live Demo and Backup Evidence

The mentor can perform the functional checks directly at [http://13.212.52.148:8080](http://13.212.52.148:8080). The screenshots or video recorded for this section are backup evidence in case the live demo is no longer available after the planned maintenance date.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/public-website.png. Caption: *Figure 5.6.1.1. TechBlog public website on EC2.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/role-test-matrix.png. Caption: *Figure 5.6.1.2. Redacted role-based test evidence.* -->
