---
title: "Network and Amazon RDS"
date: 2026-07-10
weight: 3
pre: " <b> 5.3. </b> "
---

Create `techblog-ec2-sg` for the application and temporarily allow port 8080 only from the administrator IP. Create `techblog-rds-sg` and allow MySQL port 3306 only from `techblog-ec2-sg`—never from `0.0.0.0/0`.

Create a small Single-AZ RDS for MySQL database named `techblog`, keep Public access disabled when EC2 is in the same VPC, attach the RDS security group, and store its credentials in Secrets Manager or a Parameter Store SecureString.

```text
jdbc:mysql://<rds-endpoint>:3306/techblog?useSSL=true&serverTimezone=UTC
```

Test DNS and port 3306 from EC2. For timeouts, inspect VPC placement, routes, network ACLs, and security-group references instead of making the database public. `ddl-auto=update` is acceptable for this demo; use Flyway or Liquibase for a longer-lived environment.
