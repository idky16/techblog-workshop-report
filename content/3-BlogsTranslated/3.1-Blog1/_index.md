---
title: "Amazon Q and Cisco Webex MCP Servers"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# [Amazon Q and Cisco Webex MCP Servers] Building a Meeting Prep and Follow-up Assistant

Hello AWS Study Group VN!

In this post, I would like to share an interesting solution from my exploration of Generative AI applications on AWS: building an AI assistant that helps users prepare before meetings and automatically summarize follow-up information after meetings by combining Amazon Q with Cisco Webex MCP Servers.

{{< content-image src="images/3-BlogsPosted/ML-21199-1.png" alt="Amazon Q and Cisco Webex MCP Servers meeting prep and follow-up assistant architecture" >}}

{{< content-image src="images/3-BlogsPosted/b1.jpg" alt="Facebook post about the Amazon Q and Cisco Webex MCP Servers meeting assistant" >}}

In modern enterprise environments, employees often attend many meetings every day. Reviewing previous meeting information, collecting context, and tracking unfinished action items can take a lot of time. After each meeting, users also need to write notes, summarize important decisions, and send follow-up emails or messages to relevant members. As the number of meetings increases, managing information becomes more difficult and important tasks can easily be missed.

In the AWS and Cisco blog post, the authors introduce a solution that uses Amazon Q as the central AI assistant. Users can ask natural language questions such as "Prepare me for tomorrow's meeting" or "Summarize last week's meeting". Amazon Q analyzes the request and then uses Model Context Protocol (MCP) to access data from Cisco Webex services.

The solution architecture includes three main data sources:

- **Webex Meetings MCP** provides meeting information, transcripts, and recordings.
- **Webex Messaging MCP** provides messages, spaces, and discussions.
- **Cisco Vidcast MCP** provides videos, highlights, and related transcripts.

Through these MCP Servers, Amazon Q can collect data from multiple sources and synthesize it into a complete response for the user.

After analyzing the data, the system can generate useful outputs such as a **Meeting Prep Brief** for preparation, **Meeting Summary** for meeting recap, **Key Decisions** for important decisions, **Action Items** for tasks to complete, and **Follow-up Draft** for follow-up emails or messages.

What impressed me most is how the solution uses MCP as a standard connection layer between AI and external enterprise data systems. Instead of building many separate API integrations, Amazon Q can access enterprise data through MCP Servers with OAuth authentication, user-level authorization, and activity logging to support security and governance.

In my opinion, this is a practical example showing that Generative AI is not only useful for content creation but can also become an intelligent work assistant. It can help businesses save time, improve collaboration efficiency, and make better use of existing data. The solution also demonstrates the potential of Amazon Q for building AI agents that can interact with multiple systems to support daily work.

## Reference

<https://aws.amazon.com/blogs/machine-learning/build-a-meeting-prep-and-follow-up-assistant-with-amazon-quick-and-cisco-webex-mcp-servers/>
