---
title: "Kiểm thử chức năng và phân quyền"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Mục tiêu

Kiểm tra Spring Boot MVC application đã deploy vẫn giữ đúng hành vi public, USER, EDITOR và ADMIN khi làm việc với RDS.

## Điều kiện trước khi thực hiện

- `techblog.service` ở trạng thái `active (running)`.
- Website truy cập được tại `http://<EC2_PUBLIC_IP>:8080/`.
- RDS có schema và test data đã migrate.
- Có test account riêng, không chứa dữ liệu nhạy cảm cho từng role.

## Các bước thực hiện

1. Mở public URL trong private browser window và test trang Guest.
2. Đăng ký USER mới, xác nhận account được lưu trước khi hệ thống thử gửi best-effort notification cho quản trị viên.
3. Sign in bằng USER và thực hiện Reader action.
4. Gửi Writer upgrade request.
5. Dùng ADMIN approve request, sau đó sign in bằng EDITOR.
6. Tạo post, kiểm tra trạng thái `PENDING` và test quyền edit/delete bài của mình.
7. Dùng ADMIN approve hoặc reject post, sau đó xác nhận trạng thái hiển thị.
8. Test comment/report moderation và category/tag management mà Admin UI hiện tại cung cấp.
9. Sign out sau mỗi role và xác nhận route không có quyền bị từ chối.

## Ma trận kiểm thử

| Vai trò | Kịch bản | Kết quả mong đợi |
|---|---|---|
| Guest | Home, danh sách/chi tiết bài | Public page render không cần đăng nhập |
| Guest | URL profile/editor/admin được bảo vệ | Redirect đến sign in hoặc bị từ chối |
| USER / Reader | Sign up, sign in, sign out | Session start và kết thúc đúng |
| USER / Reader | Like và save/bookmark | State được lưu trong RDS |
| USER / Reader | Comment, reply, report | Data hợp lệ được lưu và report đi vào Admin workflow |
| USER / Reader | Profile và Writer request | Profile update, request có thể review |
| EDITOR / Writer | Tạo bài | Post mới vào `PENDING` |
| EDITOR / Writer | Sửa/xóa bài của mình | Chỉ owner được phép |
| EDITOR / Writer | Sửa bài của Writer khác | Bị từ chối |
| ADMIN | Approve/reject post | Status đổi thành `APPROVED` hoặc `REJECTED` |
| ADMIN | Approve/reject Writer request | User role đổi theo quyết định |
| ADMIN | Quản lý user/category/tag/comment/report | Administration action hợp lệ thành công |

## Cấu hình

Chỉ dùng test account và test content. Không để email thật, profile data, session cookie, CSRF value hoặc password field trong screenshot.

## Cách kiểm tra

- Refresh browser sau mỗi data-changing action.
- Query hoặc kiểm tra RDS record liên quan mà không lộ user detail.
- Xác nhận unauthorized request không âm thầm thành công.
- Ghi pass/fail và evidence path cho từng dòng.

## Kết quả mong đợi

Toàn bộ role boundary và chức năng blog chính hoạt động thống nhất trên EC2/RDS deployment.

## Lỗi thường gặp

| Lỗi | Cách xử lý |
|---|---|
| Login fail sau migration | Kiểm tra imported user, password encoding, RDS connection và application log. |
| Role menu hiển thị nhưng action bị denied | So sánh stored role và Spring Security rule. |
| Post giữ `PENDING` | Hoàn thành Admin approval flow và refresh record. |
| Public website không truy cập được | Kiểm tra `systemd`, port `8080`, EC2 SG và public IP hiện tại. |

## Ghi chú chi phí và bảo mật

Dùng test dataset nhỏ và xóa khi report không còn cần. Không dùng account cá nhân giống production trong screenshot.

## Bản demo trực tiếp và bằng chứng dự phòng

Mentor có thể thực hiện kiểm thử chức năng trực tiếp tại [http://13.212.52.148:8080](http://13.212.52.148:8080). Ảnh chụp hoặc video của phần này là bằng chứng dự phòng nếu bản demo trực tiếp không còn khả dụng sau thời gian duy trì dự kiến.

<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/public-website.png. Caption: *Hình 5.6.1.1. Website TechBlog public trên EC2.* -->
<!-- TODO: Thêm /images/5-Workshop/5.6-Testing-Operations/role-test-matrix.png. Caption: *Hình 5.6.1.2. Bằng chứng role-based test đã che thông tin.* -->
