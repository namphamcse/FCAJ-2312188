---
title: "Resource Cleanup"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

NAT Gateway, ALB, RDS, and running Fargate workloads can continue to incur charges. To avoid unnecessary costs, remove resources in the dependency order below.

#### 1. CloudFront and S3 frontend

1. Disable the CloudFront distribution and wait for the deployment to finish.
2. Then delete the distribution.
3. If you no longer need the frontend, empty its S3 bucket and then delete the bucket.

#### 2. ECS service and tasks

1. Set the ECS service desired count to `0`, or delete the service if it is no longer needed.
2. Wait until all Fargate tasks have stopped.
3. Once the cluster has no remaining services or tasks, delete `shopsflow-cluster`.

#### 3. Application Load Balancer

After ECS no longer references them, delete `shopsflow-alb`, any remaining listeners or rules, and then the target group.

#### 4. ECR

Keep `shopsflow-repo` if the image is still part of the project record. For a full cleanup, delete its images first, then delete the repository.

#### 5. RDS

1. Delete `database-shopsflow`.
2. Choose whether to create a final snapshot, then review any remaining automated backups or snapshots.
3. Once no database uses it, delete `shopsflow-db-subnet-group`.

#### 6. CloudWatch

Delete `/shopsflow/ecs/backend` and any custom alarms or dashboards only when those logs and monitoring resources are no longer needed.

#### 7. IAM

Delete custom ECS execution and task roles only after ECS no longer uses them. Do not delete account roles that other projects share.

#### 8. NAT Gateways and Elastic IPs

1. Delete `shopsflow-nat-a` and `shopsflow-nat-b`.
2. Wait until both NAT Gateways have been deleted.
3. Finally, release the two unused Elastic IP addresses.

#### 9. VPC

After the ALB, RDS, ECS network interfaces, and NAT Gateways have been removed, delete the custom route tables, detach and delete `shopsflow-igw`, delete the subnets and custom security groups, and finally delete `shopsflow-vpc`.

#### 10. Billing check

Review Billing, Cost Explorer, and the relevant resource pages to make sure no lab workload is still running unexpectedly.

The workshop shows that managed services reduce host-management work, but routes, IAM permissions, health checks, payment callbacks, logs, and cost remain operational responsibilities.
