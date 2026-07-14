---
title: "Amazon Q và Cisco Webex MCP Servers"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# [Amazon Q và Cisco Webex MCP Servers] Xây dựng trợ lý AI hỗ trợ chuẩn bị và tổng kết cuộc họp

Xin chào các bạn và anh chị AWS Study Group VN!

Hôm nay mình xin chia sẻ một giải pháp khá thú vị trong quá trình tìm hiểu các ứng dụng Generative AI trên AWS: xây dựng trợ lý AI hỗ trợ chuẩn bị trước cuộc họp và tự động tổng kết sau cuộc họp bằng cách kết hợp Amazon Q với Cisco Webex MCP Servers.

{{< content-image src="images/3-BlogsPosted/ML-21199-1.png" alt="Amazon Q và Cisco Webex MCP Servers - kiến trúc trợ lý chuẩn bị và tổng kết cuộc họp" >}}

{{< content-image src="images/3-BlogsPosted/b1.jpg" alt="Bài đăng Facebook về trợ lý cuộc họp Amazon Q và Cisco Webex MCP Servers" >}}

Trong môi trường doanh nghiệp hiện nay, nhân viên thường phải tham gia nhiều cuộc họp mỗi ngày. Việc xem lại thông tin, nội dung từ các cuộc họp trước, tổng hợp những đầu việc còn tồn đọng thường tốn khá nhiều thời gian. Bên cạnh đó, sau mỗi cuộc họp, người dùng còn phải ghi chú, tổng hợp các quyết định quan trọng, gửi email hoặc tin nhắn cho các thành viên liên quan. Khi số lượng cuộc họp ngày càng tăng, việc quản lý thông tin trở nên khó khăn và rất dễ bỏ sót các nhiệm vụ quan trọng.

Trong bài viết, các chuyên gia của AWS và Cisco giới thiệu một giải pháp sử dụng Amazon Q làm trợ lý AI trung tâm. Người dùng chỉ cần đặt câu hỏi bằng ngôn ngữ tự nhiên như "Hãy chuẩn bị cho cuộc họp ngày mai" hoặc "Tóm tắt nội dung cuộc họp tuần trước". Amazon Q sẽ phân tích yêu cầu, sau đó sử dụng giao thức Model Context Protocol (MCP) để truy cập dữ liệu từ các dịch vụ Cisco Webex.

Kiến trúc giải pháp bao gồm ba nguồn dữ liệu chính:

- **Webex Meetings MCP** cung cấp thông tin cuộc họp, transcript và bản ghi.
- **Webex Messaging MCP** cung cấp tin nhắn, không gian làm việc và các cuộc thảo luận.
- **Cisco Vidcast MCP** cung cấp video, highlights và transcript liên quan.

Thông qua các MCP Servers này, Amazon Q có thể thu thập dữ liệu từ nhiều nguồn khác nhau và tổng hợp thành một câu trả lời hoàn chỉnh cho người dùng.

Sau khi phân tích dữ liệu, hệ thống có thể tạo ra nhiều loại kết quả hữu ích như **Meeting Prep Brief** để chuẩn bị trước cuộc họp, **Meeting Summary** để tóm tắt nội dung đã trao đổi, **Key Decisions** để liệt kê các quyết định quan trọng, **Action Items** để xác định công việc cần thực hiện và **Follow-up Draft** để soạn thảo email hoặc tin nhắn theo dõi sau cuộc họp.

Điểm mình thấy ấn tượng ở giải pháp này là cách các tác giả tận dụng MCP như một chuẩn kết nối giữa AI và các hệ thống dữ liệu bên ngoài. Thay vì phải xây dựng nhiều API tích hợp riêng lẻ, Amazon Q có thể truy cập dữ liệu doanh nghiệp thông qua các MCP Servers với cơ chế xác thực OAuth, phân quyền người dùng và ghi nhật ký hoạt động nhằm đảm bảo tính bảo mật và khả năng quản trị.

Theo mình, đây là một ví dụ rất thực tế cho thấy Generative AI không chỉ hỗ trợ tạo nội dung mà còn có thể trở thành trợ lý làm việc thông minh, giúp doanh nghiệp tiết kiệm thời gian, nâng cao hiệu suất cộng tác và khai thác hiệu quả nguồn dữ liệu sẵn có. Giải pháp này cũng cho thấy tiềm năng của Amazon Q trong việc xây dựng các AI Agent có khả năng tương tác với nhiều hệ thống khác nhau để hỗ trợ công việc hằng ngày.

## Nguồn tham khảo

<https://aws.amazon.com/blogs/machine-learning/build-a-meeting-prep-and-follow-up-assistant-with-amazon-quick-and-cisco-webex-mcp-servers/>
