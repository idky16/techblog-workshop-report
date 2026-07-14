---
title: "Create AWS Budget"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Objective

Create a budget alert before provisioning EC2, RDS, and S3 resources.

## Prerequisites

- Use the shared AWS account for the TTV demo.
- Sign in with an IAM identity when possible; root should be reserved for billing or recovery.

## Steps

1. Open **Billing and Cost Management**.
2. Go to **Budgets**.
3. Create a cost budget for the monthly demo limit.
4. Configure email notification for the account owner or responsible member.
5. Review and create the budget.

## Configuration

| Setting | Value |
|---|---|
| Budget type | Cost budget |
| Scope | Shared TechBlog demo account |
| Region note | Billing is global, but resources are mainly in `ap-southeast-1`. |
| Alert | Email notification before the limit is exceeded |

## Verification

Confirm that the budget appears in AWS Budgets and that the notification email is correct.

## Expected Result

AWS Budgets is configured before other resources. A budget only alerts the team; it does not automatically stop EC2, RDS, or S3.

## Common Issues

| Issue | Fix |
|---|---|
| Budget email not confirmed | Check the inbox and spam folder. |
| Budget created too late | Create it before compute and database resources. |

## Cost and Security Notes

Budget alerts reduce surprise cost but do not replace cleanup.

<!-- TODO: Add /images/5-Workshop/5.3-Budget-S3-IAM/aws-budget.png. Caption: *Figure 5.3.1.1. AWS Budget created before TechBlog resources.* -->
