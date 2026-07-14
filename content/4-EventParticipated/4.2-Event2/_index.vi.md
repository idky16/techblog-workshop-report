---
title: "Event 2"
date: 2026-06-13
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Báo cáo tổng kết: "FCAJ Community Meetup - Từ hành trình Cloud đến phát triển nghề nghiệp"

### Mục tiêu sự kiện

* Giúp sinh viên và thực tập sinh hiểu rõ hơn về công việc thực tế của một DevOps Engineer và những kiến thức nền tảng cần thiết để theo đuổi lĩnh vực này.
* Minh họa cách kết hợp các dịch vụ AWS để xây dựng một hệ thống rút gọn đường dẫn có khả năng mở rộng và bảo mật tốt.
* Chia sẻ hành trình thực tế từ chương trình First Cloud AI Journey đến việc tham gia cộng đồng AWS và làm việc tại một đối tác của AWS.
* Giúp sinh viên hiểu rõ hơn về vai trò, trách nhiệm và kỹ năng cần thiết của một Data Analytics Engineer.
* Giới thiệu quy trình tuyển dụng, văn hóa doanh nghiệp và yêu cầu nghề nghiệp tại các tập đoàn đa quốc gia.
* Khuyến khích sinh viên phát triển đồng thời kiến thức kỹ thuật và các kỹ năng chuyên môn quan trọng như tư duy phản biện, giao tiếp và giải quyết vấn đề.

### Diễn giả

* **Trong H. Truong** - "DevOps Engineer thực sự làm gì?"
* **Dinh Trung Kien và Nguyen Minh Tho** - "Xây dựng dịch vụ rút gọn URL có khả năng mở rộng trên AWS"
* **Danh Hoang Hieu Nghi** - "Từ First Cloud AI Journey đến AWS Partner"
* **Dat Pham và Cuong Nguyen** - "Câu chuyện nghề nghiệp thực tế và văn hóa tại các tập đoàn đa quốc gia"

### Nội dung nổi bật

#### DevOps Engineer thực sự làm gì?

* Phần trình bày cho thấy DevOps không chỉ đơn thuần là viết quy trình CI/CD, quản lý Docker container hay triển khai ứng dụng lên môi trường Cloud.
* Những công việc phổ biến của DevOps bao gồm Infrastructure as Code, quản lý cấu hình, container, orchestration, nền tảng Cloud, logging, monitoring và tự động hóa triển khai.
* Tuy nhiên, công việc thực tế của DevOps còn yêu cầu khả năng hiểu cách các hệ thống tương tác với nhau, xác định đúng người hoặc bộ phận chịu trách nhiệm cho một vấn đề và cải thiện sự phối hợp giữa đội phát triển và đội vận hành.
* Diễn giả khuyến nghị người học nên bắt đầu từ những kiến thức nền tảng vững chắc như Linux, networking, Git, CI/CD, Python hoặc Golang, container và cách một ứng dụng được xây dựng, cấu hình, triển khai và giám sát.
* Các dự án thực hành nhỏ được xem là một phương pháp học hiệu quả. Người học có thể tự triển khai một ứng dụng, tự động hóa một phần quy trình, giám sát hệ thống, chủ động tạo lỗi và sau đó tìm cách khắc phục.
* Phần trình bày cũng nhấn mạnh rằng sao chép câu lệnh không đồng nghĩa với việc hiểu bản chất của chúng. Kỹ sư nên đặt câu hỏi “tại sao” trước khi hỏi “làm như thế nào”.
* Một DevOps Engineer giỏi cần có tư duy hệ thống, biết tự động hóa các công việc lặp lại, giao tiếp rõ ràng và sử dụng AI như một công cụ hỗ trợ thay vì phụ thuộc hoàn toàn vào AI.

#### Xây dựng dịch vụ rút gọn URL có khả năng mở rộng trên AWS

* Các diễn giả giới thiệu mục đích cơ bản của một dịch vụ rút gọn URL, đó là chuyển một đường dẫn dài thành một liên kết ngắn và thuận tiện hơn, sau đó chuyển hướng người dùng đến địa chỉ ban đầu.
* Một kiến trúc đơn giản có thể ít tốn chi phí và dễ triển khai, nhưng vẫn có thể tồn tại nhiều hạn chế như lỗ hổng bảo mật, độ trễ cao, điểm lỗi duy nhất và khả năng mở rộng thấp.
* Kiến trúc AWS được đề xuất chia hệ thống thành các thành phần riêng biệt, bao gồm frontend, dịch vụ tạo khóa và backend, nhằm tăng khả năng bảo trì và mở rộng.
* Tầng frontend sử dụng các dịch vụ như **Amazon CloudFront**, **AWS WAF** và **AWS Amplify** để phân phối ứng dụng web và tăng cường bảo mật tại lớp biên.
* Một Key Generation Service riêng biệt được chạy trong container trên **Amazon ECS**, có nhiệm vụ tạo trước các mã rút gọn và lưu chúng vào **Amazon ElastiCache for Redis**.
* Khi người dùng tạo một URL ngắn, backend sẽ lấy một mã rút gọn có sẵn từ Redis và lưu mối quan hệ giữa mã ngắn với URL gốc trong **Amazon DynamoDB**.
* Khi người dùng truy cập liên kết ngắn, hệ thống sẽ kiểm tra Redis trước để lấy địa chỉ đích nhanh hơn. Nếu dữ liệu không có trong cache, hệ thống sẽ truy vấn DynamoDB và sau đó cập nhật lại cache.
* Kiến trúc này minh họa nhiều khái niệm quan trọng như phân tách trách nhiệm giữa các thành phần, bảo mật tại lớp biên, tạo trước mã rút gọn và mô hình cache-aside.
* Bài trình bày còn cung cấp bản demo hoạt động và mã QR để người tham dự trải nghiệm dịch vụ rút gọn URL.

#### Từ First Cloud AI Journey đến AWS Partner

* Diễn giả chia sẻ hành trình nghề nghiệp bắt đầu từ chương trình First Cloud AI Journey và tiếp tục phát triển thông qua nhiều chương trình học tập, cộng đồng và cơ hội nghề nghiệp liên quan đến AWS.
* Phần trình bày cho thấy việc có được công việc đầu tiên chỉ là bước khởi đầu của một hành trình nghề nghiệp dài hơn, với nhiều hướng phát triển như Solutions Architect, DevOps Engineer, Platform Engineer và Software Engineer.
* Sinh viên được khuyến khích chủ động “viết nên lịch sử của chính mình” bằng cách không ngừng phát triển kỹ năng thay vì chỉ chờ đợi cơ hội xuất hiện.
* **AWS Student Builder Group Program**, trước đây được biết đến với tên gọi AWS Cloud Clubs, được giới thiệu là một nền tảng giúp sinh viên học tập, tổ chức sự kiện, kết nối với những người cùng quan tâm và đóng góp cho cộng đồng AWS.
* Việc tham gia các hoạt động cộng đồng có thể mang lại kinh nghiệm thực tế, cơ hội mở rộng mối quan hệ, sự công nhận và quyền truy cập vào nhiều tài nguyên học tập.
* **AWS Community Builders Program** cũng được giới thiệu như một cơ hội dành cho những cá nhân tích cực chia sẻ kiến thức và hỗ trợ cộng đồng công nghệ.
* Phần trình bày còn giới thiệu về AWS Partners và cho thấy việc tham gia cộng đồng, học tập kỹ thuật và mở rộng mạng lưới nghề nghiệp có thể tạo ra nhiều cơ hội trong hệ sinh thái AWS.
* Một thông điệp quan trọng của bài trình bày là sinh viên không nên chỉ tập trung vào chứng chỉ kỹ thuật, mà còn cần xây dựng portfolio, tham gia cộng đồng và chia sẻ những kiến thức đã học được.

#### Data Analytics Engineering và văn hóa tại tập đoàn đa quốc gia

* Các diễn giả giải thích rằng một Data Analytics Engineer không chỉ có nhiệm vụ tạo báo cáo hoặc hiển thị các chỉ số kinh doanh.
* Trong công việc thực tế, chuyên viên dữ liệu cần phân tích nguyên nhân khiến các chỉ số như GMV thay đổi, xác định các điểm yếu trong hoạt động kinh doanh và đề xuất giải pháp cải thiện phù hợp.
* Một số công cụ và nền tảng kỹ thuật được đề cập gồm **PsychoPy**, **Discourse**, **AutoGluon** và **LightGBM**, tùy theo tính chất của từng dự án.
* Các diễn giả nhấn mạnh ba kỹ năng chuyên môn quan trọng gồm tư duy phản biện, giao tiếp và giải quyết vấn đề.
* “Storytelling with data” được trình bày như khả năng chuyển các dashboard và kết quả số liệu thành những thông tin kinh doanh rõ ràng, hỗ trợ việc đưa ra quyết định.
* Bài trình bày mô tả nhiều cấp độ phát triển nghề nghiệp khác nhau như **Follower**, **Learner**, **Problem Solver**, **System Thinker** và **Super Star**.
* Sinh viên được khuyến khích vượt qua giai đoạn chỉ làm theo hướng dẫn, sau đó từng bước học cách hiểu hệ thống, xác định vấn đề và tạo ra giá trị lớn hơn cho tổ chức.

#### Quy trình tuyển dụng và văn hóa tại các tập đoàn đa quốc gia

* Một quy trình tuyển dụng phổ biến tại các tập đoàn đa quốc gia được trình bày qua bốn giai đoạn gồm sàng lọc hồ sơ và phỏng vấn sơ bộ, kiểm tra năng lực, phỏng vấn kỹ thuật và phỏng vấn mức độ phù hợp với văn hóa doanh nghiệp.
* Hệ thống Applicant Tracking System có thể được sử dụng để tự động quét hồ sơ ứng viên trước khi ứng viên tham gia một buổi phỏng vấn ngắn bằng tiếng Anh với bộ phận tuyển dụng.
* Sau đó, ứng viên có thể phải thực hiện các bài kiểm tra về tư duy logic, chuyên môn kỹ thuật hoặc xử lý tình huống trước khi tham gia phỏng vấn sâu với trưởng nhóm kỹ thuật hoặc quản lý.
* Giai đoạn cuối cùng có thể tập trung đánh giá mức độ phù hợp giữa giá trị cá nhân, phong cách làm việc của ứng viên và văn hóa của tổ chức.
* Văn hóa doanh nghiệp được mô tả là cách mọi người trong công ty suy nghĩ, hành động và phối hợp với nhau, thay vì chỉ là những khẩu hiệu mang tính hình thức.
* Một số ví dụ được đề cập gồm văn hóa **no-blame post-mortem**, trong đó đội ngũ tập trung tìm nguyên nhân gốc rễ của sự cố hệ thống thay vì đổ lỗi cho cá nhân, và văn hóa quan tâm, hòa nhập, đặt con người và sự đa dạng vào trung tâm phát triển.
* Các diễn giả liên hệ kinh nghiệm phát triển của các quốc gia châu Á với tầm quan trọng của việc học hỏi các tiêu chuẩn quốc tế nhưng vẫn giữ gìn bản sắc địa phương.
* Phần trình bày còn điểm lại quá trình phát triển của Việt Nam từ thời kỳ kinh tế còn nhiều hạn chế, giai đoạn Đổi Mới, kết nối Internet, thu hút đầu tư nước ngoài, tham gia chuỗi cung ứng toàn cầu đến phát triển kinh tế số.
* Sinh viên được khuyến khích chuyển từ trạng thái chỉ đơn thuần “làm việc” sang “làm việc theo tiêu chuẩn”, đặc biệt là các tiêu chuẩn liên quan đến công nghệ, bảo mật, chất lượng và đạo đức nghề nghiệp.
* Thông điệp cuối cùng nhấn mạnh việc thực hiện đúng trách nhiệm của một người làm nghề, một công dân và một con người có trách nhiệm với xã hội.

### Bài học chính

* **DevOps là một tư duy, không chỉ là tập hợp công cụ**: kiến thức nền tảng, tư duy hệ thống và khả năng giao tiếp vẫn luôn quan trọng ngay cả khi công nghệ thay đổi.
* **Dự án thực tế giúp hiểu kiến thức sâu hơn**: tự triển khai, giám sát, tạo lỗi và sửa lỗi cho một hệ thống hiệu quả hơn nhiều so với chỉ sao chép câu lệnh hoặc đọc tài liệu.
* **Kiến trúc Cloud cần tính đến khả năng mở rộng và độ tin cậy**: một hệ thống hoạt động tốt với ít người dùng vẫn có thể gặp vấn đề về bảo mật, độ trễ hoặc điểm lỗi duy nhất.
* **Caching có thể cải thiện hiệu suất đáng kể**: hệ thống rút gọn URL đã minh họa cách kết hợp Redis và DynamoDB thông qua mô hình cache-aside.
* **Phát triển nghề nghiệp cần sự chủ động**: các chương trình học tập, nhóm sinh viên, hoạt động cộng đồng và việc chia sẻ kiến thức có thể tạo ra nhiều cơ hội ngoài môi trường giáo dục chính thức.
* **Có được công việc chỉ là bước khởi đầu**: sự phát triển lâu dài phụ thuộc vào khả năng học tập liên tục, xây dựng portfolio và từng bước đảm nhận những vấn đề lớn hơn.
* **Dữ liệu phải hỗ trợ quá trình ra quyết định**: người làm dữ liệu hiệu quả không chỉ hiển thị con số mà còn phải giải thích sự thay đổi, tìm nguyên nhân và truyền đạt ý nghĩa kinh doanh.
* **Kỹ năng mềm ảnh hưởng trực tiếp đến hiệu quả kỹ thuật**: giao tiếp, tư duy phản biện và giải quyết vấn đề là những kỹ năng cần thiết khi làm việc nhóm và xử lý yêu cầu kinh doanh thực tế.
* **Văn hóa doanh nghiệp ảnh hưởng đến cách giải quyết vấn đề**: một tổ chức lành mạnh tập trung vào hệ thống và nguyên nhân gốc rễ thay vì đổ lỗi cho cá nhân.
* **Tiêu chuẩn toàn cầu và giá trị địa phương có thể cùng tồn tại**: sinh viên nên học hỏi các tiêu chuẩn nghề nghiệp quốc tế đồng thời sử dụng kiến thức để đóng góp cho sự phát triển của Việt Nam.

### Áp dụng vào công việc

* Củng cố kiến thức về Linux, networking, Git, container và CI/CD trước khi cố gắng sử dụng quá nhiều công cụ DevOps nâng cao cùng một lúc.
* Khi triển khai dự án thực tập trên AWS, tập trung hiểu vai trò của từng dịch vụ và lý do sử dụng chúng thay vì chỉ làm theo các bước cấu hình.
* Sử dụng kiến trúc hệ thống rút gọn URL như một tài liệu tham khảo khi thiết kế ứng dụng Cloud có các tầng frontend, backend, caching và cơ sở dữ liệu riêng biệt.
* Quan tâm đến bảo mật và hiệu suất ngay từ đầu bằng cách sử dụng các dịch vụ như CloudFront, AWS WAF và giải pháp caching, thay vì chỉ bổ sung chúng sau khi hệ thống đã hoàn thành.
* Áp dụng mô hình cache-aside khi dự án cần truy xuất dữ liệu thường xuyên mà không gửi mọi yêu cầu trực tiếp đến cơ sở dữ liệu chính.
* Thực hiện các bài thử nghiệm nhỏ, trong đó tự triển khai, giám sát và xử lý lỗi ứng dụng để hiểu toàn bộ quy trình vận hành.
* Cải thiện báo cáo thực tập bằng cách không chỉ trình bày những gì đã triển khai mà còn giải thích lý do lựa chọn từng giải pháp kiến trúc và kỹ thuật.
* Tham gia tích cực hơn vào các cộng đồng học tập AWS và chương trình dành cho sinh viên để học hỏi từ những người khác và mở rộng mạng lưới nghề nghiệp.
* Xem dự án thực tập như một phần trong portfolio nghề nghiệp thay vì chỉ là một yêu cầu học tập.
* Khi trình bày kết quả dự án, áp dụng phương pháp storytelling with data: trình bày vấn đề, đưa ra bằng chứng, xác định nguyên nhân và diễn giải rõ giá trị của giải pháp.
* Luyện tập trả lời phỏng vấn bằng những ví dụ thực tế trong quá trình thực tập, đặc biệt là các tình huống liên quan đến sự cố kỹ thuật, làm việc nhóm và quyết định dự án.
* Áp dụng cách tiếp cận không đổ lỗi khi dự án nhóm xuất hiện lỗi, bằng cách tìm điểm yếu trong quy trình, cấu hình hoặc kiến trúc thay vì tập trung vào cá nhân gây ra sai sót.
* Đánh giá chất lượng dự án theo các tiêu chuẩn chuyên nghiệp như bảo mật, tài liệu, khả năng bảo trì và độ tin cậy.

### Trải nghiệm sự kiện

Việc tham dự FCAJ Community Meetup đã giúp tôi có góc nhìn rộng hơn về cả nghề nghiệp kỹ thuật lẫn sự phát triển chuyên môn.

* Phần trình bày về DevOps hữu ích vì đã giúp tôi điều chỉnh quan niệm phổ biến rằng DevOps chỉ là người viết pipeline triển khai hoặc quản lý Docker và Kubernetes. Tôi hiểu rõ hơn rằng DevOps còn liên quan đến tư duy hệ thống, giao tiếp, tự động hóa và trách nhiệm cải thiện cách đội nhóm phát triển và vận hành phần mềm.
* Tôi đặc biệt ấn tượng với lời khuyên về việc học kiến thức nền tảng. Trong quá trình làm dự án kỹ thuật, người học rất dễ sao chép các câu lệnh để ứng dụng hoạt động mà không thực sự hiểu hệ điều hành, mạng hoặc quy trình triển khai phía sau.
* Bài trình bày về hệ thống rút gọn URL là một trong những nội dung kỹ thuật có tính thực tế cao nhất. Nội dung cho thấy cách nhiều dịch vụ AWS được kết hợp thành một kiến trúc hoàn chỉnh thay vì chỉ được học riêng lẻ.
* Việc so sánh giữa một hệ thống rút gọn URL đơn giản và một kiến trúc AWS có khả năng mở rộng giúp tôi hiểu rõ hơn lý do một ứng dụng có thể cần đến CloudFront, AWS WAF, ECS, Redis và DynamoDB dù chức năng cơ bản của nó khá đơn giản.
* Hành trình của diễn giả từ First Cloud AI Journey đến cộng đồng AWS và AWS Partner tạo động lực cho tôi vì cho thấy việc tham gia cộng đồng có thể mang lại kiến thức, mối quan hệ và cơ hội nghề nghiệp thực tế.
* Tôi cũng nhận ra rằng chứng chỉ thôi là chưa đủ. Dự án, portfolio, kỹ năng giao tiếp và đóng góp cho cộng đồng đều là những bằng chứng quan trọng về năng lực thực tế.
* Phần trình bày về Data Analytics Engineering giúp tôi hiểu rằng làm việc với dữ liệu không chỉ là tạo báo cáo. Giá trị thực sự nằm ở khả năng giải thích nguyên nhân khiến chỉ số kinh doanh thay đổi và hỗ trợ người ra quyết định xác định hành động tiếp theo.
* Nội dung về quy trình tuyển dụng tại tập đoàn đa quốc gia giúp tôi hình dung rõ hơn những gì sinh viên có thể gặp sau khi tốt nghiệp, đặc biệt là vòng phỏng vấn tiếng Anh, bài kiểm tra chuyên môn và đánh giá mức độ phù hợp với văn hóa doanh nghiệp.
* Phần thảo luận về văn hóa doanh nghiệp và no-blame post-mortem có liên hệ trực tiếp với quá trình làm việc nhóm. Nội dung khuyến khích tôi tập trung nhiều hơn vào nguyên nhân gốc rễ và cải tiến quy trình khi dự án gặp vấn đề.
* Phần cuối về tiêu chuẩn nghề nghiệp và trách nhiệm đã mang lại ý nghĩa rộng hơn cho toàn bộ sự kiện. Nội dung liên kết sự phát triển nghề nghiệp cá nhân với trách nhiệm tạo ra sản phẩm đạt tiêu chuẩn quốc tế và đóng góp tích cực cho xã hội.

#### Một số hình ảnh sự kiện

![Hình ảnh minh chứng](/images/4-EventParticipated/Event02-01.JPG)
![Hình ảnh minh chứng](/images/4-EventParticipated/Event02-02.JPG)

> Nhìn chung, FCAJ Community Meetup đã kết nối hiệu quả nhiều chủ đề gồm kiến trúc Cloud, DevOps, phân tích dữ liệu, phát triển nghề nghiệp và văn hóa doanh nghiệp. Sự kiện không chỉ mang lại kiến thức thực tế về AWS và kỹ thuật, mà còn giúp tôi hiểu rõ hơn về tư duy, kỹ năng giao tiếp và tiêu chuẩn nghề nghiệp cần thiết để phát triển từ một sinh viên trở thành một chuyên gia công nghệ có trách nhiệm.
