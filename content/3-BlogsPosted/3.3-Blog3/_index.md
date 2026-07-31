---
title: "NAT Gateway, Glacier, PassRole, and EBS: four AWS traps"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

When I first started learning AWS, I encountered familiar concepts such as EC2, S3, RDS, and standard architectures on slides. Only when operating real systems did I realize that some less-discussed details can lead to significant costs or incidents at the least convenient time.

The following are four practical lessons about AWS operational mechanisms that affect cost, security, and incident response.

### 1. NAT Gateway and the cost of working with S3

Putting EC2 instances in a private subnet and using a NAT Gateway for Internet access is a common architecture. However, when an application frequently reads or writes S3 data—for logging, backups, or media uploads—this design can create unnecessary costs.

**The issue:** Under a default route, traffic from EC2 to S3 can pass through a NAT Gateway. NAT Gateway has an hourly charge and a data-processing charge for every GB that passes through it.

**The consequence:** The bill can increase substantially due to NAT Gateway data-processing fees even when the EC2 instance and S3 bucket are in the same Region.

**How to handle it:** Create an **S3 Gateway VPC Endpoint** for the VPC. This endpoint does not have an additional usage charge and lets S3 traffic use the AWS internal network instead of the NAT Gateway. It improves the path and removes NAT Gateway data-processing charges for S3 traffic.

### 2. Moving small files to Glacier can cost more than storage

S3 Glacier and Glacier Deep Archive are suited to low-cost, long-term storage. However, applying a Lifecycle Rule that moves every object to Glacier after a period of time can be counterproductive when a bucket holds millions of small files.

**Transition cost:** Lifecycle Transition Requests are charged per object. At a very large file count, the transition-request cost can exceed the storage savings.

**Metadata cost:** Objects stored in Glacier have additional management metadata. A file that is only a few KB can therefore be billed for significantly more storage than its original data size.

**How to handle it:** Before creating a Lifecycle Rule, package small files into larger archives with `zip` or `tar`, or set a condition to transition only objects above a minimum size, such as `128 KB`.

### 3. Privilege-escalation risk from `iam:PassRole`

To make deployments convenient, developers are sometimes granted `iam:PassRole` with the wildcard `*`, allowing them to attach IAM roles to services such as Lambda or EC2. This permission requires strict control.

**A dangerous scenario:** An account with permission to create Lambda functions and `iam:PassRole` for every role can attach a role that already has `AdministratorAccess` to a Lambda function. The function can then perform actions with administrator permissions, resulting in privilege escalation.

**How to handle it:** Do not use a wildcard for `iam:PassRole`. Specify the exact ARNs of roles that may be passed and restrict the target service when appropriate. Treat `iam:PassRole` with a level of care similar to administrative permission.

### 4. The six-hour limit when modifying an EBS volume

Elastic Volumes allow you to increase capacity or change a volume type, such as from `gp2` to `gp3`, on a running EC2 instance. Although convenient, the operation has an important timing constraint: after modifying a volume, you must wait at least six hours before modifying the same volume again.

**A practical scenario:** When a disk is nearly full, you might quickly increase it from `100 GB` to `120 GB`, then realize that `500 GB` is needed. If the prior modification has not passed the required waiting period, AWS will not allow another immediate change.

**How to handle it:** Estimate capacity with a safety margin on the first modification and monitor usage with CloudWatch so it can be addressed before an incident occurs.

### Conclusion

Working in the cloud is not only about assembling services according to an architecture diagram. Details such as traffic routes, object size, IAM permissions, and volume-modification limits may not appear in basic labs, but they can determine system reliability and operational cost safety.

I hope these notes help others avoid invisible AWS traps.

### Blog Link

[View the blog post on Facebook](https://www.facebook.com/share/p/18wTHQFBVC/)
