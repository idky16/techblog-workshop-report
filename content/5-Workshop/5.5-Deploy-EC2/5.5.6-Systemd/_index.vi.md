---
title: "Chạy TechBlog bằng systemd"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.5.6. </b> "
---

## Mục tiêu

Chạy TechBlog như persistent Linux service để ứng dụng start cùng máy, restart khi lỗi và tiếp tục hoạt động sau khi đóng SSH.

## Điều kiện trước khi thực hiện

- JAR tồn tại tại `/home/ec2-user/techblog.jar`.
- `/etc/techblog/techblog.env` tồn tại với mode `600`.
- `ec2-user` ghi được vào `/var/log/techblog`.

## Các bước thực hiện

Tạo `/etc/systemd/system/techblog.service`:

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

Giữ `ExecStart` trên một dòng. Sau đó load và start unit:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now techblog
sudo systemctl status techblog --no-pager
curl -I http://localhost:8080
```

Sau khi local check thành công, mở:

```text
http://<EC2_PUBLIC_IP>:8080/
```

## Cấu hình

| Hạng mục | Giá trị |
|---|---|
| Unit file | `/etc/systemd/system/techblog.service` |
| Environment file | `/etc/techblog/techblog.env` |
| Service user | `ec2-user` |
| Executable | `/home/ec2-user/techblog.jar` |
| Restart policy | `Restart=always` |
| Application port | `8080` |

## Cách kiểm tra

1. `systemctl status` hiển thị `active (running)`.
2. `curl` trả HTTP 200 sau khi ứng dụng start xong.
3. Đóng SSH, kết nối lại và xác nhận service vẫn active.
4. Chỉ reboot khi lịch demo cho phép, sau đó xác nhận enabled service tự start.

## Kết quả mong đợi

TechBlog chạy độc lập với SSH shell và đọc toàn bộ runtime integration qua restricted environment file.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| `status=217/USER` | Kiểm tra service user/group. |
| Java command bị tách | Giữ toàn bộ `ExecStart` trên một dòng. |
| Service restart loop | Đọc `journalctl -u techblog` và kiểm tra RDS/runtime value. |
| HTTP check quá sớm | Chờ Spring Boot start và đọc journal. |
| Public URL fail nhưng local curl pass | Kiểm tra EC2 inbound TCP `8080` và public IP hiện tại. |

## Ghi chú chi phí và bảo mật

Không in environment file trong screenshot. Public port `8080` chỉ tạm thời; stop instance hoặc giới hạn rule sau demo.

<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/systemd-active.png. Caption: *Hình 5.5.6.1. techblog.service ở trạng thái active (running).* -->
<!-- TODO: Thêm /images/5-Workshop/5.5-Deploy-EC2/curl-http-200.png. Caption: *Hình 5.5.6.2. Kiểm tra HTTP 200 local trên EC2.* -->
