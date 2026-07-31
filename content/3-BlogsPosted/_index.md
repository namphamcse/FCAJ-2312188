---
title: "Published Blogs"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section collects the AWS blogs I have written, focusing on hands-on experience and technical issues that can arise when operating systems.

### [Blog 1 – S3, IMDSv2, and Lambda: three AWS operational issues to avoid](3.1-Blog1/)

This article covers three easily overlooked technical details: incomplete multipart uploads in S3, the IMDSv2 hop limit for containerized applications, and reuse of the AWS Lambda `/tmp` directory.

### [Blog 2 – Cross-AZ, MTU, DynamoDB, and Logs Insights: AWS operational risks](3.2-Blog2/)

This article examines four operational risks: cross-AZ data-transfer costs, MTU across VPC Peering/VPN, DynamoDB On-Demand scaling limits, and CloudWatch Logs Insights scan costs.

### [Blog 3 – NAT Gateway, Glacier, PassRole, and EBS: four AWS traps](3.3-Blog3/)

This article discusses four operational traps involving NAT Gateway, S3 Glacier, `iam:PassRole`, and EBS Elastic Volumes that can directly affect cost, security, and incident response.
