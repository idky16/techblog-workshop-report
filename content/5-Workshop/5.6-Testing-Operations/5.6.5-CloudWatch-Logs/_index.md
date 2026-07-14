---
title: "Configure and Verify CloudWatch Logs"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.6.5. </b> "
---

## Objective

Use CloudWatch Agent to send the Spring Boot file log from EC2 to a centralized Log Group with a 14-day retention period.

## Prerequisites

- Local file `/var/log/techblog/application.log` exists and contains only suitable events.
- `techblog-ec2-role` has CloudWatch Agent/Logs permissions.
- Region is `ap-southeast-1`.

## Steps

### 1. Create the Log Group

1. Open **Amazon CloudWatch → Logs → Log groups**.
2. Confirm Log Group `/techblog/application` uses log class **Standard**.
3. Confirm retention is **14 days**.
4. Do not store passwords, JWTs, cookies, authorization headers, full email addresses, or uploaded content in the log.

### 2. Install CloudWatch Agent

```bash
sudo dnf install -y amazon-cloudwatch-agent
```

### 3. Create the Agent Configuration

Create `/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json`:

```json
{
  "agent": {
    "region": "ap-southeast-1",
    "run_as_user": "root"
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/techblog/application.log",
            "log_group_name": "/techblog/application",
            "log_stream_name": "{instance_id}",
            "retention_in_days": 14
          }
        ]
      }
    }
  }
}
```

### 4. Start and Check the Agent

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status
```

## Configuration

| Item | Value |
|---|---|
| Source file | `/var/log/techblog/application.log` |
| Log Group | `/techblog/application` |
| Log class | Standard |
| Log stream | Current EC2 instance ID via `{instance_id}` |
| Region | `ap-southeast-1` |
| Retention | 14 days |
| Collected severity | Suitable INFO, WARN, and ERROR events |

## Verification

1. Open one normal TechBlog page to generate a fresh safe event.
2. Wait for the agent delivery interval.
3. Open `/techblog/application`, choose the current stream, and find the new timestamp.
4. Compare the CloudWatch event with the local file event.
5. Confirm the event does not contain secret or personal data.

## Expected Result

The implemented CloudWatch pipeline displays current TechBlog application events under the EC2 instance-ID stream; Log Group class is Standard and retention is 14 days.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Agent status is stopped | Validate JSON and restart through `amazon-cloudwatch-agent-ctl`. |
| Log stream is missing | Check IAM permission, Region, source file path, and agent log. |
| No new events | Generate a local event and verify the source file timestamp first. |
| AccessDenied | Review `techblog-ec2-role` CloudWatch Logs resources/actions. |
| Duplicate ingestion | Confirm only one collect entry points to the application file. |

Agent diagnostics:

```bash
sudo tail -n 100 /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log
```

## Cost and Security Notes

Fourteen-day retention limits storage cost. Avoid DEBUG-level collection in the demo and never centralize sensitive values.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/cloudwatch-log-group.png. Caption: *Figure 5.6.5.1. /techblog/application with 14-day retention.* -->
<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/cloudwatch-log-events.png. Caption: *Figure 5.6.5.2. Redacted TechBlog application events in CloudWatch Logs.* -->
