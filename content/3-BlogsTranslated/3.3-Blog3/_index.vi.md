---
title: "Amazon Bedrock AgentCore"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# [Amazon Bedrock AgentCore] Xây dựng hệ thống Multi-Agent cho lĩnh vực tài chính trên Amazon EKS

Xin chào các bạn và anh chị AWS Study Group VN!

Hôm nay mình xin chia sẻ một bài viết khá thú vị vừa được đăng trên AWS Industries Blog về cách xây dựng hệ thống Multi-Agent dành cho lĩnh vực tài chính bằng cách kết hợp Amazon EKS và Amazon Bedrock AgentCore.

{{< content-image src="images/3-BlogsPosted/725904867_3853524508288501_6898272658705177036_n.jpg" alt="Amazon Bedrock AgentCore - kiến trúc Multi-Agent cho lĩnh vực tài chính trên Amazon EKS" >}}

{{< content-image src="images/3-BlogsPosted/b2.jpg" alt="Bài đăng Facebook về hệ thống Multi-Agent cho tài chính sử dụng Amazon Bedrock AgentCore" >}}

Trong các tổ chức tài chính, một yêu cầu từ khách hàng hiếm khi chỉ cần một chuyên gia xử lý. Chẳng hạn, khi một khách hàng muốn đánh giá danh mục đầu tư, hệ thống có thể cần kết hợp nhiều chuyên môn khác nhau như phân tích thị trường, đánh giá rủi ro, tư vấn đầu tư và truy xuất dữ liệu tài chính. Nếu tất cả công việc đều do một AI Agent đảm nhiệm thì khả năng xử lý sẽ bị giới hạn và chất lượng câu trả lời cũng khó đạt được độ chính xác cao.

Để giải quyết bài toán này, các chuyên gia AWS đã giới thiệu một kiến trúc Multi-Agent, trong đó mỗi AI Agent đảm nhận một vai trò chuyên biệt và phối hợp với nhau để hoàn thành yêu cầu của người dùng.

Kiến trúc được triển khai trên Amazon EKS. Ở trung tâm hệ thống là Agent Gateway, đóng vai trò tiếp nhận yêu cầu và điều phối các AI Agent phù hợp. Thay vì một mô hình AI phải xử lý toàn bộ quy trình, hệ thống chia thành nhiều Agent với từng nhiệm vụ riêng như Financial Advisor, Portfolio Analyst, Risk Assessment, Market Data và Financial Tools thông qua Model Context Protocol (MCP).

Sau khi Agent Gateway phân tích yêu cầu, các Agent sẽ phối hợp để thu thập dữ liệu, thực hiện suy luận và trao đổi kết quả với nhau trước khi tạo ra câu trả lời cuối cùng cho người dùng. Toàn bộ quá trình suy luận được thực hiện thông qua Amazon Bedrock, đồng thời AgentCore cung cấp thêm các khả năng như Memory để lưu ngữ cảnh hội thoại, Code Interpreter để thực thi mã khi cần xử lý dữ liệu và Browser để truy cập các nguồn thông tin bên ngoài.

Một điểm mình thấy khá hay trong kiến trúc này là việc AWS tách riêng từng AI Agent theo từng miền nghiệp vụ thay vì xây dựng một Agent "biết làm mọi thứ". Cách tiếp cận này giúp mỗi Agent chỉ tập trung vào một chuyên môn cụ thể, dễ mở rộng, dễ bảo trì và có thể thay thế hoặc cập nhật độc lập mà không ảnh hưởng đến toàn bộ hệ thống. Đây cũng là mô hình đang được nhiều doanh nghiệp áp dụng khi phát triển các hệ thống AI Agent quy mô lớn.

Ngoài các AI Agent, kiến trúc còn sử dụng LiteLLM Proxy để chuẩn hóa việc gọi các mô hình ngôn ngữ lớn, ArgoCD để triển khai ứng dụng theo mô hình GitOps và Crossplane để tự động quản lý hạ tầng trên Kubernetes. Nhờ đó, hệ thống không chỉ giải quyết bài toán AI mà còn hướng tới khả năng vận hành và mở rộng trong môi trường production.

Theo mình, điểm đáng học hỏi nhất từ bài viết này không nằm ở việc sử dụng thật nhiều dịch vụ AWS mà là cách thiết kế hệ thống theo mô hình "chia để trị". Thay vì kỳ vọng một AI có thể xử lý mọi nhiệm vụ, kiến trúc Multi-Agent chia nhỏ bài toán thành nhiều phần, giao cho các Agent có chuyên môn riêng và để một Agent điều phối tổng hợp kết quả. Điều này vừa giúp tăng chất lượng phản hồi, vừa tạo điều kiện để mở rộng hệ thống khi xuất hiện các yêu cầu hoặc nghiệp vụ mới.

Trong thời gian tới, mô hình Multi-Agent nhiều khả năng sẽ xuất hiện ngày càng nhiều trong các hệ thống AI doanh nghiệp, không chỉ trong lĩnh vực tài chính mà còn ở chăm sóc khách hàng, y tế, sản xuất hay quản trị nội bộ. Amazon Bedrock AgentCore vì vậy được xem là một trong những nền tảng đáng chú ý dành cho các tổ chức muốn xây dựng và vận hành các AI Agent ở quy mô lớn.

## Nguồn tham khảo

<https://aws.amazon.com/blogs/industries/multi-agent-systems-for-financial-services-on-amazon-eks-and-agentcore/>
