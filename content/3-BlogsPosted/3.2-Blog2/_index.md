---
title: "Cross-AZ, MTU, DynamoDB, and Logs Insights: AWS operational risks"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

After working with AWS for a while, I have found a large gap between certificate material and what happens in production. Some incidents create no obvious alert and are not listed among common errors, yet they can make a system unstable or generate substantial service costs.

The following are four overlooked technical issues that are important when operating systems on AWS.

### 1. Cross-AZ data transfer: network costs inside the same Region

Many people know that data transfer to the Internet costs money, but assume that internal traffic in the same Region is always free. In practice, AWS can charge for data transferred between Availability Zones in the same Region. A common rate is `USD 0.01/GB` in each direction, or `USD 0.02/GB` for a full data-transfer cycle.

**A practical scenario:** An EKS cluster or EC2-based microservice system is distributed across multiple AZs for availability. Services continually call each other or query a Redis cache/database fixed in one AZ. Cross-AZ traffic accumulated over thousands of requests per second can create terabytes of transfer and make the monthly bill higher than compute costs.

**How to handle it:** Design with **AZ-awareness**. In Kubernetes, mechanisms such as Topology Aware Hints can prefer routing traffic between workloads in the same AZ; traffic should cross AZ boundaries only when needed for fault tolerance.

### 2. The MTU 9001 trap over VPC Peering or VPN

This issue is difficult to debug because systems often produce no clear logs. EC2 instances in the same VPC can use Jumbo Frames with an MTU of `9001` bytes. However, traffic through inter-Region VPC Peering, VPN, or some hybrid connections can be limited to an MTU of `1500` bytes.

**Symptoms:** Ping works and SSH connects normally, but requests that transfer a large file or a long JSON API payload can hang until they time out.

**Why it happens:** Packets larger than the path MTU need Path MTU Discovery to work correctly. If an intermediate device needs to send an ICMP message about the packet-size limit but ICMP is blocked by a security group or network ACL, the message never reaches the sender. This is commonly called a Path MTU Discovery Black Hole.

**How to handle it:** Allow the required ICMP traffic, such as Custom ICMP - IPv4 Type 3, Code 4 (Destination Unreachable), within the appropriate security boundary. Another option is to reduce the network-interface MTU to `1500` when a workload depends heavily on VPC-to-VPC or VPN connectivity.

### 3. DynamoDB On-Demand scaling limits during a flash sale

DynamoDB On-Demand is convenient because it does not require pre-calculating WCU/RCU. However, this does not mean a table can reach every traffic level immediately. DynamoDB On-Demand is based on traffic history and needs time to adapt to sharply increased load.

**A risk scenario:** A system normally processes about `1,000` requests per second, then a flash sale pushes traffic to `20,000` requests per second within seconds. If the increase is too rapid compared with historical traffic, requests can be throttled and the application can receive errors such as `ProvisionedThroughputExceededException`.

**How to handle it:** For a known high-traffic event, load test and plan capacity in advance. You can switch to Provisioned Capacity with suitable WCU/RCU, or increase load gradually so DynamoDB has time to adapt, then reassess whether to return to On-Demand.

### 4. An expensive click in CloudWatch Logs Insights

CloudWatch Logs Insights is convenient for querying logs, but its price is not based on the number of result lines. It is based on the total raw log volume scanned. A common rate is about `USD 0.005` per GB scanned.

**A common mistake:** Open a Log Group containing six months of logs, around `500 GB`, and run a broad query with no time filter. One run can scan all data and cost roughly `USD 2.5`. If a similar query runs every five minutes to feed a dashboard, log-scan costs can increase very quickly.

**How to handle it:** Always use the smallest possible time range, such as the last 15 minutes or hour. For large-scale historical log analysis, export logs to S3 and use Amazon Athena to optimize query costs.

Working in the cloud is not only about assembling services according to an architecture diagram. It also requires understanding the operational parameters beneath the infrastructure. Details such as MTU, partition-scaling behavior, and cross-AZ charges may be absent from basic labs, but they can determine a system's cost and reliability.

I hope these notes help others avoid invisible incidents and operate systems more effectively.

### Blog Link

[View the blog post on Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2230167921081501/?rdid=OWE359AjcB0vTUf2#)
