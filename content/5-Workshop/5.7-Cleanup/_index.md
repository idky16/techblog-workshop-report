---
title: "Cleanup"
date: 2026-07-10
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Objective

Remove or intentionally retain every TechBlog AWS resource after the final evidence has been collected. Cleanup protects the shared TTV account and its credits from continuing demo charges.

## Prerequisites

- Required screenshots and command outputs have been saved and redacted.
- The group has decided whether RDS data or a final snapshot must be retained.
- All four members agree that the final demo is complete.

## Cleanup Order

Follow dependency order instead of deleting items randomly:

1. Stop application activity and save final evidence.
2. Terminate EC2 and review EBS.
3. Delete RDS after the snapshot decision.
4. Empty and delete S3.
5. Remove CloudWatch Logs, the CPU alarm, and the SES identity if no longer required.
6. Detach/delete IAM policies and role.
7. Delete unused security groups.
8. Review Budgets, Billing, and every Region.

## Steps

### 1. EC2 and EBS

- Stop `techblog.service` if the instance is still running.
- Stop EC2 for a short planned pause or terminate `techblog-ec2` after the final demo.
- Confirm the root EBS volume follows the intended delete-on-termination setting.
- Delete any unattached EBS volume or snapshot that the group does not need.
- Check for an unused Elastic IP even though this workshop does not require one.

### 2. RDS and Snapshots

- Stop `techblog-db` only for a temporary pause; stopping is not a permanent cleanup strategy.
- Before deletion, decide whether to create the final snapshot required by the report.
- If no final snapshot is needed, explicitly choose the appropriate delete option.
- Delete obsolete manual snapshots after confirming no recovery requirement.

### 3. S3 Objects and Bucket

- Delete test objects from `avatars/` and `posts/`.
- Check for incomplete multipart uploads or versioned objects if settings changed during testing.
- Empty `techblog-uploads-ttv-2026`, then delete the bucket.
- Do not make the bucket public merely to simplify cleanup.

### 4. Amazon SES

- Delete the verified sender identity only if the group will not continue using it.
- Confirm there is no active test configuration that can continue sending.
- No Dedicated IP, Mail Manager, Global Endpoint, or attachment resource should exist for this workshop.

### 5. CloudWatch

- Stop/uninstall CloudWatch Agent if EC2 is retained for another purpose.
- Delete Log Group `/techblog/application` when evidence and logs are no longer required.
- Delete `techblog-high-cpu`.
- Review any accidental extra log group, alarm, or dashboard.

### 6. IAM and Security Groups

- Detach the instance profile, `AmazonS3FullAccess`, `CloudWatchAgentServerPolicy`, and inline `TechBlogSesSendEmail` after EC2 no longer uses them.
- Delete `techblog-ec2-role` only after all dependencies are removed.
- Delete unused EC2/RDS security groups; default security groups remain.
- Confirm no access key was created for the application.

### 7. Budgets and Billing Review

- Keep AWS Budgets while the project/account remains active, or delete it after the workshop if no longer needed.
- Review Billing/Cost Explorer after cleanup.
- Check `ap-southeast-1` and other Regions for accidental resources.

## Final Checklist

- [ ] EC2 is terminated or intentionally stopped.
- [ ] No unintended EBS volume or Elastic IP remains.
- [ ] RDS is deleted/stopped according to the plan.
- [ ] Final snapshot decision is documented.
- [ ] S3 objects and bucket are removed when no longer needed.
- [ ] SES identity is removed or intentionally retained.
- [ ] CloudWatch Agent, Log Group, and `techblog-high-cpu` are removed when no longer needed.
- [ ] IAM policies/role and custom security groups are cleaned up.
- [ ] AWS Budgets and Billing have been reviewed.
- [ ] Other Regions contain no accidental resources.

## Expected Result

The shared AWS account has no unintended chargeable TechBlog resource. Every retained item has an owner, purpose, and planned removal date.

## Troubleshooting

| Issue | Resolution |
|---|---|
| S3 bucket cannot be deleted | Empty all objects, versions, and incomplete multipart uploads. |
| Security group is in use | Remove dependent EC2/RDS/network interfaces first. |
| IAM Role cannot be deleted | Detach instance profile and managed/customer policies. |
| RDS deletion is blocked | Review deletion protection and final-snapshot requirements carefully. |
| Cost remains after cleanup | Check delayed billing, snapshots, EBS, Elastic IP, logs, and other Regions. |

## Cost and Security Notes

Deleting evidence too early can harm the report; retaining resources indefinitely creates cost. Save redacted evidence first, then clean up in dependency order.

<!-- TODO: Add /images/5-Workshop/5.7-Cleanup/cleanup-checklist.png. Caption: *Figure 5.7.1. Final TechBlog cleanup and Billing review.* -->
