---
title: "Run TechBlog with systemd"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.5.6. </b> "
---

## Objective

Run TechBlog as a persistent Linux service so it starts on boot, restarts after a failure, and continues after the SSH session closes.

## Prerequisites

- JAR exists at `/home/ec2-user/techblog.jar`.
- `/etc/techblog/techblog.env` exists with mode `600`.
- `/var/log/techblog` is writable by `ec2-user`.

## Steps

Create `/etc/systemd/system/techblog.service`:

```ini
[Unit]
Description=TechBlog Spring Boot Application
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=ec2-user
Group=ec2-user
WorkingDirectory=/home/ec2-user
EnvironmentFile=/etc/techblog/techblog.env
ExecStart=/usr/bin/java -jar /home/ec2-user/techblog.jar
Restart=always
RestartSec=5
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

Keep `ExecStart` on one line. Then load and start the unit:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now techblog
sudo systemctl status techblog --no-pager
curl -I http://localhost:8080
```

After the local check succeeds, open:

```text
http://<EC2_PUBLIC_IP>:8080/
```

## Configuration

| Item | Value |
|---|---|
| Unit file | `/etc/systemd/system/techblog.service` |
| Environment file | `/etc/techblog/techblog.env` |
| Service user | `ec2-user` |
| Executable | `/home/ec2-user/techblog.jar` |
| Restart policy | `Restart=always` |
| Application port | `8080` |

## Verification

1. `systemctl status` shows `active (running)`.
2. `curl` returns HTTP 200 after the application finishes starting.
3. Close SSH, reconnect, and confirm the service remains active.
4. Reboot only if the demo schedule permits, then confirm the enabled service starts automatically.

## Expected Result

TechBlog runs independently of the SSH shell and reads all runtime integrations through the restricted environment file.

## Troubleshooting

| Issue | Resolution |
|---|---|
| `status=217/USER` | Check the service user/group. |
| Java command is split | Keep the entire `ExecStart` value on one line. |
| Service enters a restart loop | Read `journalctl -u techblog` and verify RDS/runtime values. |
| HTTP check is too early | Wait for Spring Boot startup and read the journal. |
| Public URL fails but local curl works | Check EC2 inbound TCP `8080` and current public IP. |

## Cost and Security Notes

Do not print the environment file in screenshots. Public port `8080` is temporary; stop the instance or restrict the rule after the demo.

<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/systemd-active.png. Caption: *Figure 5.5.6.1. techblog.service active (running).* -->
<!-- TODO: Add /images/5-Workshop/5.5-Deploy-EC2/curl-http-200.png. Caption: *Figure 5.5.6.2. Local HTTP 200 verification on EC2.* -->
