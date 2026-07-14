---
title: "Cấu hình và kiểm tra CloudWatch Logs"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.6.5. </b> "
---

## Mục tiêu

Dùng CloudWatch Agent gửi Spring Boot file log từ EC2 đến centralized Log Group với retention 14 ngày.

## Điều kiện trước khi thực hiện

- Local file `/var/log/techblog/application.log` tồn tại và chỉ có event phù hợp.
- `techblog-ec2-role` có CloudWatch Agent/Logs permission.
- Region là `ap-southeast-1`.

## Các bước thực hiện

### 1. Tạo Log Group

1. Mở **Amazon CloudWatch → Logs → Log groups**.
2. Xác nhận Log Group `/techblog/application` dùng log class **Standard**.
3. Xác nhận retention là **14 days**.
4. Không ghi password, JWT, cookie, authorization header, email đầy đủ hoặc nội dung upload vào log.

### 2. Cài CloudWatch Agent

```bash
sudo dnf install -y amazon-cloudwatch-agent
```

### 3. Tạo Agent configuration

Tạo `/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json`:

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

### 4. Start và kiểm tra Agent

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status
```

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| Source file | `/var/log/techblog/application.log` |
| Log Group | `/techblog/application` |
| Log class | Standard |
| Log stream | EC2 instance ID hiện tại qua `{instance_id}` |
| Region | `ap-southeast-1` |
| Retention | 14 ngày |
| Severity được thu | Event INFO, WARN và ERROR phù hợp |

## Cách kiểm tra

1. Mở một trang TechBlog bình thường để tạo safe event mới.
2. Chờ agent delivery interval.
3. Mở `/techblog/application`, chọn stream hiện tại và tìm timestamp mới.
4. So sánh CloudWatch event với event trong local file.
5. Xác nhận event không chứa secret hoặc personal data.

## Kết quả mong đợi

CloudWatch pipeline đã triển khai hiển thị TechBlog application event mới trong stream theo EC2 instance ID; Log Group dùng class Standard và retention 14 ngày.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Agent status stopped | Validate JSON và restart bằng `amazon-cloudwatch-agent-ctl`. |
| Thiếu log stream | Kiểm tra IAM permission, Region, source file path và agent log. |
| Không có event mới | Tạo local event và kiểm tra source file timestamp trước. |
| AccessDenied | Review CloudWatch Logs resource/action của `techblog-ec2-role`. |
| Ingestion trùng | Xác nhận chỉ một collect entry trỏ đến application file. |

Agent diagnostics:

```bash
sudo tail -n 100 /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log
```

## Ghi chú chi phí và bảo mật

Retention 14 ngày giới hạn storage cost. Tránh thu DEBUG-level trong demo và không centralize giá trị nhạy cảm.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/cloudwatch-log-group.png. Caption: *Hình 5.6.5.1. /techblog/application với retention 14 ngày.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/cloudwatch-log-events.png. Caption: *Hình 5.6.5.2. TechBlog application event đã che thông tin trong CloudWatch Logs.* -->
