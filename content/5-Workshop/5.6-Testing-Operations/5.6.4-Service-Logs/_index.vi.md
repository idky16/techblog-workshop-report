---
title: "Kiểm tra systemd và application log local"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.6.4. </b> "
---

## Mục tiêu

Kiểm tra EC2 process, listening port, systemd journal và Spring Boot log file ở local trước khi chẩn đoán centralized CloudWatch.

## Điều kiện trước khi thực hiện

- SSH đến `techblog-ec2` hoạt động.
- TechBlog được quản lý bằng `systemd`.
- Đã cấu hình `LOGGING_FILE_NAME=/var/log/techblog/application.log`.

## Các bước thực hiện

Kiểm tra service state:

```bash
sudo systemctl status techblog --no-pager
```

Đọc service journal event gần nhất:

```bash
sudo journalctl -u techblog -n 80 --no-pager
```

Kiểm tra Spring Boot log file:

```bash
sudo ls -lh /var/log/techblog/application.log
sudo tail -n 80 /var/log/techblog/application.log
```

Kiểm tra port và Java process:

```bash
sudo ss -lntp | grep 8080
ps -ef | grep '[t]echblog.jar'
curl -I http://localhost:8080
```

## Cấu hình

| Hạng mục | Giá trị mong đợi |
|---|---|
| Service | `techblog.service` |
| Service state | `active (running)` |
| Java artifact | `/home/ec2-user/techblog.jar` |
| Listening port | `8080` |
| File log | `/var/log/techblog/application.log` |
| Local journal | `journalctl -u techblog` |

## Cách kiểm tra

1. Truy cập một trang không nhạy cảm trên trình duyệt.
2. Xác nhận application event mới xuất hiện ở local.
3. Kiểm tra event bình thường dùng level INFO/WARN/ERROR phù hợp.
4. Tìm trong sample hiển thị xem có password, authorization header, cookie, token hoặc personal data đầy đủ không.
5. Che endpoint/identity detail trước screenshot.

## Kết quả mong đợi

Nhóm chứng minh được Java process đang chạy, port `8080` đang listen, HTTP trả 200 và source log file có event an toàn cho CloudWatch Agent.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Service inactive | Đọc journal, kiểm tra JAR path và runtime variable rồi restart. |
| Thiếu port 8080 | Chờ startup hoặc kiểm tra binding/startup exception. |
| Log file không tồn tại | Kiểm tra `LOGGING_FILE_NAME`, directory owner và active JAR configuration. |
| Journal có log nhưng file rỗng | Xác nhận Spring Boot file logging bật và có quyền ghi. |
| Log có dữ liệu nhạy cảm | Sửa log statement/filter trước khi bật centralized collection. |

## Ghi chú chi phí và bảo mật

Local log không tạo CloudWatch ingestion cost nhưng vẫn dùng disk. Cấu hình rotation nếu demo chạy dài ngày.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/systemd-local-logs.png. Caption: *Hình 5.6.4.1. Service active, port 8080 và application log local đã che thông tin.* -->
