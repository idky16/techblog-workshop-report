---
title: "Event 2"
date: 2026-06-13
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Summary Report: "FCAJ Community Meetup - From Cloud Journey to Professional Growth"

### Event Objectives

- Provide students and interns with a realistic understanding of the DevOps Engineer role and the foundations required to pursue it.
- Demonstrate how AWS services can be combined to design a scalable and secure URL shortening system.
- Share a practical career journey from participating in the First Cloud AI Journey program to becoming involved in AWS community programs and working with an AWS Partner.
- Help students understand the responsibilities of a Data Analytics Engineer and the skills required to work effectively with data.
- Introduce recruitment processes, workplace culture and professional expectations in multinational corporations.
- Encourage students to develop both technical knowledge and essential professional skills such as critical thinking, communication and problem-solving.

### Speakers

- **Trong H. Truong** - "What Does a DevOps Engineer Really Do?"
- **Dinh Trung Kien and Nguyen Minh Tho** - "A Scalable URL Shortening Service on AWS"
- **Danh Hoang Hieu Nghi** - "From First Cloud AI Journey to AWS Partner"
- **Dat Pham and Cuong Nguyen** - "Real-World Career Stories and Culture in Multinational Corporations"

### Key Highlights

#### What Does a DevOps Engineer Really Do?

- The session explained that DevOps is much broader than writing CI/CD pipelines, managing Docker containers or deploying applications to cloud environments.
- Common DevOps responsibilities include infrastructure as code, configuration management, containers, orchestration, cloud platforms, logging, monitoring and deployment automation.
- However, real DevOps work also requires understanding how systems interact, identifying the actual owner of a problem and improving collaboration between development and operations teams.
- The speaker recommended starting with strong fundamentals, including Linux, networking, Git, CI/CD, Python or Golang, containers and an understanding of how applications are built, configured, deployed and monitored.
- Small practical projects were encouraged as an effective learning method: deploy an application, automate part of the process, monitor it, intentionally break it and then learn how to fix it.
- The session also emphasized that copying commands is not the same as understanding them. Engineers should ask “why” before asking “how.”
- A good DevOps Engineer should think in systems, automate repetitive work, communicate clearly and use AI to enhance their abilities rather than replacing independent thinking.

#### A Scalable URL Shortening Service on AWS

- The speakers introduced the basic purpose of a URL shortener: converting a long URL into a shorter and more convenient link that redirects users to the original address.
- A simple architecture may be inexpensive and easy to deploy, but it can introduce vulnerabilities, high latency, a single point of failure and limited scalability.
- The proposed AWS architecture separated the frontend, key-generation service and backend services to improve maintainability and scalability.
- The frontend layer used services such as **Amazon CloudFront**, **AWS WAF** and **AWS Amplify** to deliver the web application and provide protection at the edge.
- A dedicated Key Generation Service ran in containers on **Amazon ECS** and generated short codes before storing them in **Amazon ElastiCache for Redis**.
- For URL creation, the backend retrieved an available short code from Redis and stored the mapping between the short code and the original URL in **Amazon DynamoDB**.
- For URL redirection, the system first checked Redis to obtain the destination quickly. When the data was not available in the cache, it queried DynamoDB and then updated the cache.
- The design demonstrated several important architectural concepts, including separation of concerns, edge security, pre-generated short codes and the cache-aside pattern.
- A working preview and QR code were included to demonstrate the URL shortening service.

#### From First Cloud AI Journey to AWS Partner

- The speaker shared a career path beginning with the First Cloud AI Journey program and continuing through several AWS learning and community opportunities.
- The presentation showed that obtaining a first job is only the beginning of a longer professional journey, with possible career directions such as Solutions Architect, DevOps Engineer, Platform Engineer and Software Engineer.
- Students were encouraged to “write their own history” by actively developing their skills instead of waiting for opportunities to appear.
- The **AWS Student Builder Group Program**, previously known as AWS Cloud Clubs, was introduced as a platform through which students can learn, organize events, connect with other builders and contribute to the AWS community.
- Participation in community activities can provide practical experience, networking opportunities, recognition and access to learning resources.
- The **AWS Community Builders Program** was presented as another opportunity for individuals who actively share knowledge and support technical communities.
- The session also introduced AWS Partners and demonstrated how community participation, technical learning and professional networking can contribute to career opportunities within the AWS ecosystem.
- A key message from the presentation was that students should not only acquire technical certifications, but also build portfolios, participate in communities and share what they learn.

#### Data Analytics Engineering and Culture in Multinational Corporations

- The speakers explained that a Data Analytics Engineer does more than produce reports or display business figures.
- In real work, data professionals must investigate why indicators such as GMV change, identify weaknesses in business operations and propose suitable improvements.
- Important technical tools mentioned in the presentation included Python libraries and platforms such as **PsychoPy**, **Discourse**, **AutoGluon** and **LightGBM**, depending on the nature of the project.
- The speakers emphasized three essential professional skills: critical thinking, communication and problem-solving.
- “Storytelling with data” was presented as the ability to transform dashboards and numerical results into clear business insights that support decision-making.
- The presentation described different career-development mindsets, including the **Follower**, **Learner**, **Problem Solver**, **System Thinker** and **Super Star** stages.
- Students were encouraged to move beyond merely following instructions and gradually learn to understand systems, identify problems and create broader value for an organization.

#### Recruitment and Corporate Culture at Multinational Companies

- A typical recruitment process at multinational corporations was presented in four stages: resume screening and preliminary interviews, ability assessments, technical interviews and cultural-fit interviews.
- Automated Applicant Tracking Systems may first scan a candidate’s resume before a short English interview with a recruiter.
- Candidates may then complete logical, technical or situational assessments before meeting a technical lead or manager for a deeper professional interview.
- The final stage may involve evaluating whether the candidate’s values and working style match the organization.
- Corporate culture was described as the way people within a company think, behave and work together, rather than merely a set of slogans.
- Examples included a **no-blame post-mortem culture**, in which teams focus on finding the root cause of system failures instead of blaming individuals, and a **caring and inclusive culture**, which places people and diversity at the center of development.
- The speakers connected Asian development experiences with the importance of learning international standards while preserving local identity.
- The session also reviewed Vietnam’s development from economic isolation and the Đổi Mới period to Internet connectivity, foreign investment, global supply chains and the digital economy.
- Students were encouraged to progress from simply “working” to “working according to standards,” especially standards related to technology, security, quality and professional ethics.
- The final message emphasized the philosophy of doing the right work as a professional, a citizen and a responsible human being.

### Key Takeaways

- **DevOps is a mindset, not only a collection of tools**: strong fundamentals, systems thinking and communication remain valuable even when specific technologies change.
- **Practical projects create deeper understanding**: deploying, monitoring, breaking and repairing a system is more effective than only copying commands or reading documentation.
- **Cloud architecture must account for scale and reliability**: a system that works for a few users may still have security, latency and single-point-of-failure problems.
- **Caching can significantly improve performance**: the URL shortener demonstrated how Redis and DynamoDB can be combined through the cache-aside pattern.
- **Career development requires active participation**: technical programs, student groups, community activities and knowledge sharing can create opportunities beyond formal education.
- **Getting a job is only the beginning**: long-term growth depends on continuous learning, building a portfolio and gradually taking responsibility for larger problems.
- **Data must support decisions**: effective data professionals do not simply display numbers; they explain changes, identify causes and communicate business meaning.
- **Soft skills directly affect technical performance**: communication, critical thinking and problem-solving are necessary when working in teams and dealing with real business requirements.
- **Corporate culture influences how problems are solved**: a healthy organization focuses on systems and root causes rather than blaming individuals.
- **Global standards and local values can coexist**: students should learn international professional standards while using their knowledge to contribute to Vietnam’s development.

### Applying to Work

- Strengthen my understanding of Linux, networking, Git, containers and CI/CD before attempting to use too many advanced DevOps tools at the same time.
- When deploying the internship project on AWS, focus on understanding the role of each service and the reason it is included rather than only following configuration instructions.
- Apply the URL-shortener architecture as a reference for designing cloud applications with separate frontend, backend, caching and database layers.
- Consider security and performance from the beginning by using services such as CloudFront, AWS WAF and caching solutions instead of treating them as later additions.
- Use the cache-aside concept when the project needs to retrieve frequently accessed information without sending every request directly to the primary database.
- Create small experiments in which I deploy, monitor and troubleshoot an application so that I can understand the complete operational process.
- Improve the final internship report by explaining not only what was implemented, but also why each architectural and technical decision was made.
- Participate more actively in AWS learning communities and student programs to gain knowledge from other builders and expand my professional network.
- Treat the internship project as part of a professional portfolio rather than only an academic requirement.
- When presenting project results, use data storytelling: show the problem, explain the evidence, identify the cause and clearly communicate the value of the proposed solution.
- Practice answering interview questions using real examples from the internship, especially situations involving technical problems, teamwork and project decisions.
- Apply a no-blame approach when bugs occur in the team project by identifying weaknesses in the process, configuration or architecture instead of focusing on who made the mistake.
- Review the quality of the project according to professional standards, including security, documentation, maintainability and reliability.

### Event Experience

Attending this FCAJ Community Meetup gave me a broader view of both technical careers and professional development.

- The DevOps session was useful because it corrected the common assumption that DevOps is simply a person who writes deployment pipelines or manages Docker and Kubernetes. It helped me understand that DevOps also involves systems thinking, communication, automation and responsibility for improving the way a team delivers software.
- I found the advice about learning fundamentals particularly relevant. During technical projects, it is easy to copy commands that make the application work without fully understanding the network, operating system or deployment process behind them.
- The URL-shortening presentation was one of the most practical technical sessions. It showed how multiple AWS services could be combined into a complete architecture instead of being studied separately.
- The comparison between a simple URL-shortening flow and a scalable AWS architecture helped me understand why an application may need CloudFront, AWS WAF, ECS, Redis and DynamoDB even when its basic function appears simple.
- The speaker’s journey from the First Cloud AI Journey program to the AWS community and an AWS Partner was motivating because it demonstrated that community participation can lead to real learning, connections and career opportunities.
- I also learned that certifications alone are not enough. Projects, portfolios, communication and contributions to the community are important evidence of practical ability.
- The Data Analytics Engineering session helped me see that working with data is not limited to creating reports. The real value comes from explaining why a business indicator changes and helping decision-makers determine what to do next.
- The section on multinational-company recruitment provided a clearer picture of what students may encounter after graduation, particularly English screening interviews, technical assessments and cultural-fit discussions.
- The discussion about corporate culture and no-blame post-mortems was relevant to teamwork. It encouraged me to focus more on root causes and process improvements whenever a project problem occurs.
- The final discussion about professional standards and responsibility gave the event a wider meaning. It connected personal career development with the responsibility to create work that meets international standards and contributes positively to society.

#### Some event photos

![Image proof](/images/4-EventParticipated/Event02-01.JPG)
![Image proof](/images/4-EventParticipated/Event02-02.JPG)

> Overall, this FCAJ Community Meetup successfully connected cloud architecture, DevOps, data analytics, career development and corporate culture. The event provided not only practical AWS and engineering knowledge, but also a clearer understanding of the mindset, communication skills and professional standards needed to grow from a student into a responsible technology professional.