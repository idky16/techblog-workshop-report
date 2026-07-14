---
title: "Check systemd and Local Application Logs"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.6.4. </b> "
---

## Objective

Verify the local EC2 process, listening port, systemd journal, and Spring Boot log file before diagnosing centralized CloudWatch behavior.

## Prerequisites

- SSH access to `techblog-ec2` works.
- TechBlog is managed by `systemd`.
- `LOGGING_FILE_NAME=/var/log/techblog/application.log` is configured.

## Steps

Check service state:

```bash
sudo systemctl status techblog --no-pager
```

Read recent service journal events:

```bash
sudo journalctl -u techblog -n 80 --no-pager
```

Check the Spring Boot log file:

```bash
sudo ls -lh /var/log/techblog/application.log
sudo tail -n 80 /var/log/techblog/application.log
```

Check the port and Java process:

```bash
sudo ss -lntp | grep 8080
ps -ef | grep '[t]echblog.jar'
curl -I http://localhost:8080
```

## Configuration

| Item | Expected value |
|---|---|
| Service | `techblog.service` |
| Service state | `active (running)` |
| Java artifact | `/home/ec2-user/techblog.jar` |
| Listening port | `8080` |
| File log | `/var/log/techblog/application.log` |
| Local journal | `journalctl -u techblog` |

## Verification

1. Access one non-sensitive page in the browser.
2. Confirm a corresponding new application event appears locally.
3. Check that normal events use appropriate INFO/WARN/ERROR levels.
4. Search the visible sample for accidental passwords, authorization headers, cookies, tokens, or complete personal data.
5. Redact endpoint/identity details before screenshots.

## Expected Result

The team can prove that the Java process is running, port `8080` is listening, HTTP returns 200, and the source log file contains safe events for CloudWatch Agent.

## Troubleshooting

| Issue | Resolution |
|---|---|
| Service is inactive | Read the journal, verify JAR path and runtime variables, then restart. |
| Port 8080 is missing | Wait for startup or inspect the binding/startup exception. |
| Log file does not exist | Check `LOGGING_FILE_NAME`, directory ownership, and active JAR configuration. |
| Journal has logs but file is empty | Confirm Spring Boot file logging is enabled and writable. |
| Sensitive data appears | Change log statements/filters before enabling centralized collection. |

## Cost and Security Notes

Local logs do not create CloudWatch ingestion cost, but disk usage must still be controlled. Configure rotation if the demo runs for an extended period.

<!-- TODO: Add /images/5-Workshop/5.6-Testing-Operations/systemd-local-logs.png. Caption: *Figure 5.6.4.1. Active service, port 8080, and redacted local application log.* -->
