---
title: "Create and Verify the CloudWatch CPU Alarm"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.6.6. </b> "
---

## Objective

Create and verify the single CloudWatch alarm deployed for TechBlog: `techblog-high-cpu`. The workshop does not claim a status-check alarm or an SNS notification action.

## Prerequisites

- `techblog-ec2` is running in `ap-southeast-1`.
- Standard EC2 `CPUUtilization` metrics are visible in CloudWatch.
- The instance ID is known but will be redacted in screenshots.

## Steps

1. Open **Amazon CloudWatch → Alarms → All alarms**.
2. Choose **Create alarm → Select metric**.
3. Select **EC2 → Per-Instance Metrics → CPUUtilization** for `techblog-ec2`.
4. Configure:
   - Statistic: **Average**
   - Period: **5 minutes**
   - Threshold type: **Static**
   - Condition: **Greater than 70%**
   - Datapoints to alarm: **2 out of 2**, equivalent to 10 minutes
   - Missing data: **Treat missing data as good/not breaching**
5. Leave the notification action empty because Amazon SNS is not deployed.
6. Name the alarm `techblog-high-cpu` and create it.

## Configuration

| Setting | Verified value |
|---|---|
| Alarm | `techblog-high-cpu` |
| Metric | EC2 `CPUUtilization` |
| Statistic | Average |
| Threshold | Greater than 70% |
| Period | 5 minutes |
| Evaluation | 2 of 2 datapoints, 10 minutes total |
| Missing data | Good / not breaching |
| Notification action | None |

## Verification

1. Open the alarm and compare its name, metric, EC2 dimension, statistic, threshold, period, evaluation count, and missing-data setting.
2. Confirm the alarm shows recent metric data.
3. Record its current state exactly as shown; do not fabricate `OK` or `ALARM`.
4. Do not create artificial load solely to force the state to change.
5. Confirm there is no text stating that CloudWatch sends email directly through SES or that SNS is configured.

## Expected Result

`techblog-high-cpu` monitors sustained CPU usage for the correct EC2 instance. The team reviews the state in CloudWatch Console because the alarm has no notification action.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Alarm shows Insufficient data | Confirm the instance/Region and wait for two five-minute periods. |
| Wrong instance dimension | Edit or recreate the alarm with `techblog-ec2`. |
| Evaluation is not 2 of 2 | Set two evaluation periods and two datapoints to alarm. |
| No email arrives | This is expected; no SNS notification action is configured. |

## Cost and Security Notes

Keep only the verified useful alarm and remove it during cleanup. Do not expose the EC2 instance ID in screenshots.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/cloudwatch-alarm-cpu.png. Caption: *Figure 5.6.6.1. Redacted techblog-high-cpu metric, 70% threshold, 5-minute period, and 2-of-2 evaluation.* -->
