---
title: "AWS Verified Access và AWS Network Firewall"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# [AWS Verified Access và AWS Network Firewall] Xây dựng mô hình Zero Trust thay thế VPN truyền thống

Khi nhắc đến truy cập từ xa vào hệ thống nội bộ, đa số chúng ta sẽ nghĩ ngay đến VPN. Tuy nhiên, trong bối cảnh các cuộc tấn công mạng ngày càng tinh vi và mô hình làm việc hybrid trở nên phổ biến, VPN dần bộc lộ nhiều hạn chế về khả năng kiểm soát truy cập.

{{< content-image src="images/3-BlogsPosted/726261910_2233973207438101_1157835197418497774_n.jpg" alt="AWS Verified Access và AWS Network Firewall - kiến trúc Zero Trust thay thế VPN truyền thống" >}}

{{< content-image src="images/3-BlogsPosted/b3.jpg" alt="Bài đăng Facebook về kiến trúc Zero Trust sử dụng AWS Verified Access và AWS Network Firewall" >}}

Trong một bài viết gần đây trên AWS Networking & Content Delivery Blog, các chuyên gia AWS đã giới thiệu cách kết hợp AWS Verified Access và AWS Network Firewall để xây dựng kiến trúc Zero Trust, giúp kiểm soát truy cập theo đúng nguyên tắc "Never Trust, Always Verify". Sau khi đọc bài viết, mình thấy đây là một giải pháp khá hay nên muốn chia sẻ lại những nội dung nổi bật đến mọi người.

Trong nhiều năm qua, VPN gần như là giải pháp mặc định để nhân viên truy cập vào hệ thống nội bộ khi làm việc từ xa. Tuy nhiên, sau khi người dùng đăng nhập thành công, VPN thường cấp quyền truy cập vào một phạm vi mạng khá rộng. Điều này làm tăng bề mặt tấn công và khiến doanh nghiệp khó kiểm soát chính xác người dùng đang được phép truy cập tài nguyên nào.

Để giải quyết vấn đề này, AWS giới thiệu kiến trúc kết hợp giữa AWS Verified Access và AWS Network Firewall nhằm triển khai mô hình Zero Trust. Thay vì mặc định tin tưởng người dùng sau khi đăng nhập, mọi yêu cầu truy cập đều phải được xác thực và kiểm tra trước khi được phép tiếp cận ứng dụng.

Trong kiến trúc này, AWS Verified Access đóng vai trò là lớp kiểm soát truy cập đầu tiên. Dịch vụ sẽ xác minh danh tính người dùng, trạng thái thiết bị cũng như các chính sách bảo mật đã được cấu hình trước khi cho phép thiết lập kết nối đến ứng dụng. Nhờ đó, người dùng chỉ có thể truy cập đúng ứng dụng mà họ được cấp quyền thay vì toàn bộ mạng nội bộ như mô hình VPN truyền thống.

Sau khi vượt qua bước xác thực, lưu lượng tiếp tục được chuyển đến AWS Network Firewall để thực hiện kiểm tra ở tầng mạng. Firewall có khả năng phân tích lưu lượng HTTP và TCP, áp dụng các chính sách bảo mật, thực hiện Deep Packet Inspection (DPI) và phát hiện các lưu lượng bất thường hoặc độc hại trước khi chuyển tiếp đến ứng dụng hoặc cơ sở dữ liệu phía sau.

Nhìn vào kiến trúc tổng quan của bài viết, luồng xử lý diễn ra theo từng bước khá rõ ràng:

- Người dùng gửi yêu cầu truy cập đến ứng dụng.
- AWS Verified Access xác thực danh tính và đánh giá chính sách truy cập.
- Lưu lượng sau đó được chuyển đến AWS Network Firewall để kiểm tra.
- Firewall áp dụng các quy tắc bảo mật và phân tích lưu lượng mạng.
- Chỉ những kết nối hợp lệ mới được phép truy cập vào ứng dụng hoặc tài nguyên phía sau.

AWS cũng giới thiệu hai mô hình triển khai phù hợp với từng quy mô hệ thống.

Với **Distributed Deployment**, AWS Network Firewall được triển khai ngay trong VPC chứa ứng dụng. Mô hình này giúp giảm độ trễ, đồng thời cho phép mỗi workload áp dụng chính sách bảo mật riêng mà không ảnh hưởng đến các hệ thống khác.

Trong khi đó, **Centralized Deployment** tập trung toàn bộ Firewall vào một Inspection VPC để kiểm tra lưu lượng của nhiều VPC hoặc nhiều tài khoản AWS khác nhau. Đây là cách triển khai phù hợp với các doanh nghiệp lớn khi cần quản lý chính sách bảo mật một cách tập trung và nhất quán trên toàn bộ hạ tầng.

Điểm mình thấy khá hay ở giải pháp này là AWS không chỉ tập trung vào việc xác thực người dùng mà còn bổ sung thêm một lớp kiểm tra lưu lượng mạng trước khi cho phép truy cập tài nguyên.

Hai dịch vụ hoạt động bổ sung cho nhau: AWS Verified Access chịu trách nhiệm xác minh "ai đang truy cập", còn AWS Network Firewall đảm nhiệm việc kiểm tra "lưu lượng đó có an toàn hay không". Cách tiếp cận này giúp giảm đáng kể nguy cơ truy cập trái phép ngay cả khi thông tin đăng nhập của người dùng bị lộ.

Theo mình, đây là một kiến trúc rất đáng tham khảo đối với các doanh nghiệp đang chuyển dần từ mô hình VPN truyền thống sang Zero Trust. Không chỉ giúp tăng cường bảo mật, giải pháp còn mang lại khả năng kiểm soát truy cập chi tiết hơn, dễ mở rộng trong môi trường nhiều VPC hoặc nhiều tài khoản AWS và phù hợp với xu hướng hiện đại hóa hạ tầng doanh nghiệp trên cloud.

## Nguồn tham khảo

<https://aws.amazon.com/vi/blogs/networking-and-content-delivery/securing-zero-trust-access-with-aws-verified-access-and-aws-network-firewall/>
