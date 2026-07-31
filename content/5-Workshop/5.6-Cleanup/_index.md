---
title: "Resource Cleanup"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

NAT Gateway, ALB, RDS, and running Fargate workloads can continue to incur charges. Remove resources in dependency order.

#### 1. CloudFront and S3 frontend

1. Disable the CloudFront distribution and wait for deployment to finish.
2. Delete the distribution.
3. Empty the frontend S3 bucket and delete it if it is no longer required.

#### 2. ECS service and tasks

1. Set the ECS service desired count to `0` or delete the service.
2. Wait for all Fargate tasks to stop.
3. Delete `shopsflow-cluster` when it has no remaining services or tasks.

#### 3. Application Load Balancer

Delete `shopsflow-alb`, remaining listeners or rules, and the target group after ECS no longer references it.

#### 4. ECR

Keep `shopsflow-repo` if the image is part of the project record. For a full cleanup, delete its images first, then delete the repository.

#### 5. RDS

1. Delete `database-shopsflow`.
2. Choose whether to create a final snapshot and review remaining automated backups or snapshots.
3. Delete `shopsflow-db-subnet-group` when no database still uses it.

#### 6. CloudWatch

Delete `/shopsflow/ecs/backend` and any custom alarms or dashboards only when the logs and monitoring resources are no longer needed.

#### 7. IAM

Delete custom ECS execution and task roles only after ECS no longer uses them. Do not delete account roles shared by other projects.

#### 8. NAT Gateways and Elastic IPs

1. Delete `shopsflow-nat-a` and `shopsflow-nat-b`.
2. Wait until both NAT Gateways are deleted.
3. Release the two unused Elastic IP addresses.

#### 9. VPC

After the ALB, RDS, ECS network interfaces, and NAT Gateways have been removed, delete custom route tables, detach and delete `shopsflow-igw`, delete subnets and custom security groups, then delete `shopsflow-vpc`.

#### 10. Billing check

Review Billing, Cost Explorer, and the relevant resource pages to confirm that no lab workload remains active unexpectedly.

The workshop shows that managed services reduce host-management work, but routes, IAM permissions, health checks, payment callbacks, logs, and cost remain operational responsibilities.
