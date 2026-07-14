---
title: "Amazon Bedrock AgentCore"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# [Amazon Bedrock AgentCore] Building a Multi-Agent System for Financial Services on Amazon EKS

Hello AWS Study Group VN!

Today I would like to share an interesting article from the AWS Industries Blog about building a Multi-Agent system for financial services by combining Amazon EKS and Amazon Bedrock AgentCore.

{{< content-image src="images/3-BlogsPosted/725904867_3853524508288501_6898272658705177036_n.jpg" alt="Amazon Bedrock AgentCore Multi-Agent architecture for financial services on Amazon EKS" >}}

{{< content-image src="images/3-BlogsPosted/b2.jpg" alt="Facebook post about an Amazon Bedrock AgentCore Multi-Agent system for financial services" >}}

In financial institutions, a customer request rarely requires only one specialist. For example, when a customer wants to evaluate an investment portfolio, the system may need to combine multiple types of expertise such as market analysis, risk assessment, investment advisory, and financial data retrieval. If all tasks are handled by a single AI Agent, the system's capability can be limited and the quality of the answer may not reach the expected accuracy.

To address this challenge, AWS experts introduced a Multi-Agent architecture in which each AI Agent takes a specialized role and collaborates with other agents to complete the user's request.

The architecture runs on Amazon EKS. At the center of the system is the Agent Gateway, which receives requests and orchestrates the appropriate AI Agents. Instead of having one AI model handle the entire process, the system is divided into multiple agents with specific responsibilities, such as Financial Advisor, Portfolio Analyst, Risk Assessment, Market Data, and Financial Tools through Model Context Protocol (MCP).

After the Agent Gateway analyzes the request, the agents collaborate to collect data, perform reasoning, and exchange results before generating the final response for the user. The reasoning process is powered by Amazon Bedrock, while AgentCore adds capabilities such as Memory for conversation context, Code Interpreter for data processing, and Browser for accessing external information sources.

What I find interesting in this architecture is that AWS separates AI Agents by business domain instead of building one agent that "knows everything". This approach allows each agent to focus on a specific expertise, making the system easier to scale, maintain, replace, or update independently without affecting the whole architecture. This is also a pattern many enterprises are adopting when building large-scale AI Agent systems.

Beyond the AI Agents, the architecture also uses LiteLLM Proxy to standardize large language model calls, ArgoCD to deploy applications with GitOps, and Crossplane to manage infrastructure automatically on Kubernetes. As a result, the system addresses not only the AI problem but also production operations and scalability.

In my opinion, the most valuable lesson from this article is not about using many AWS services, but about designing a system with a "divide and conquer" mindset. Instead of expecting one AI to handle every task, the Multi-Agent architecture breaks the problem into smaller parts, assigns them to specialized agents, and lets an orchestrator synthesize the results. This improves response quality and makes it easier to extend the system when new requirements or business domains appear.

In the near future, Multi-Agent models will likely appear more often in enterprise AI systems, not only in financial services but also in customer support, healthcare, manufacturing, and internal operations. Amazon Bedrock AgentCore is therefore a notable platform for organizations that want to build and operate AI Agents at scale.

## Reference

<https://aws.amazon.com/blogs/industries/multi-agent-systems-for-financial-services-on-amazon-eks-and-agentcore/>
