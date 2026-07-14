---
title: "AWS Verified Access and AWS Network Firewall"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# [AWS Verified Access and AWS Network Firewall] Building a Zero Trust Model to Replace Traditional VPN

When people think about remote access to internal systems, VPN is usually the first solution that comes to mind. However, as cyberattacks become more sophisticated and hybrid work becomes more common, VPNs reveal many limitations in access control.

{{< content-image src="images/3-BlogsPosted/726261910_2233973207438101_1157835197418497774_n.jpg" alt="AWS Verified Access and AWS Network Firewall Zero Trust architecture replacing traditional VPN" >}}

{{< content-image src="images/3-BlogsPosted/b3.jpg" alt="Facebook post about AWS Verified Access and AWS Network Firewall Zero Trust architecture" >}}

In a recent AWS Networking & Content Delivery Blog post, AWS experts introduced how to combine AWS Verified Access and AWS Network Firewall to build a Zero Trust architecture based on the principle "Never Trust, Always Verify". After reading the post, I found the solution practical and worth sharing.

For many years, VPN has been the default way for employees to access internal systems while working remotely. However, after a user logs in successfully, VPN often grants access to a broad network range. This increases the attack surface and makes it difficult for organizations to precisely control which resources each user can access.

To solve this problem, AWS introduces an architecture that combines AWS Verified Access and AWS Network Firewall to implement Zero Trust. Instead of trusting users by default after login, every access request must be authenticated and evaluated before reaching the application.

In this architecture, AWS Verified Access acts as the first access control layer. It verifies user identity, device posture, and configured security policies before allowing a connection to the application. As a result, users can access only the specific applications they are authorized for, instead of the whole internal network as in a traditional VPN model.

After authentication, traffic is forwarded to AWS Network Firewall for network-layer inspection. The firewall can analyze HTTP and TCP traffic, apply security policies, perform Deep Packet Inspection (DPI), and detect abnormal or malicious traffic before forwarding it to the application or backend database.

The overall architecture follows a clear flow:

- The user sends an access request to the application.
- AWS Verified Access authenticates identity and evaluates access policies.
- Traffic is then forwarded to AWS Network Firewall for inspection.
- The firewall applies security rules and analyzes network traffic.
- Only valid connections are allowed to reach the application or backend resources.

AWS also describes two deployment models for different system scales.

With **Distributed Deployment**, AWS Network Firewall is deployed directly inside the VPC that hosts the application. This model reduces latency and allows each workload to apply its own security policy without affecting other systems.

With **Centralized Deployment**, firewalls are centralized in an Inspection VPC to inspect traffic from multiple VPCs or AWS accounts. This approach is suitable for larger enterprises that need centralized and consistent security policy management across the infrastructure.

What I find interesting about this solution is that AWS does not only focus on user authentication. It also adds another network inspection layer before allowing traffic to reach protected resources.

The two services complement each other: AWS Verified Access verifies "who is accessing", while AWS Network Firewall checks "whether the traffic is safe". This approach significantly reduces unauthorized access risk, even if user credentials are exposed.

In my opinion, this architecture is a valuable reference for organizations moving from traditional VPN to Zero Trust. It improves security, provides more granular access control, scales well across multiple VPCs or AWS accounts, and aligns with modern cloud infrastructure modernization.

## Reference

<https://aws.amazon.com/vi/blogs/networking-and-content-delivery/securing-zero-trust-access-with-aws-verified-access-and-aws-network-firewall/>
