---
title: "IAM, CloudWatch, Payment Flow, and Validation"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### IAM roles for ECS

Use separate roles for the Fargate workload:

* **Task execution role:** pulls the backend image from ECR and delivers container logs to CloudWatch.
* **Task role:** contains only permissions required by the application code inside the container.

Keep these roles separate and do not place static AWS access keys inside the container.

#### CloudWatch Logs

Configure the ECS task definition with the `awslogs` driver so the backend sends stdout and stderr to the log group:

```text
/shopsflow/ecs/backend
```

Review logs for task starts and stops, Spring Boot startup failures, database connection errors, payment errors, HTTP `4xx`/`5xx` responses, and a new deployment revision.

#### Metrics to monitor

* **ECS:** running task count, deployment status, and stopped reasons.
* **ALB:** healthy/unhealthy host count, request count, target response time, and `4xx`/`5xx` metrics.
* **RDS:** CPU utilization, database connections, and storage or memory metrics.

#### Payment flow

1. The user checks out through the frontend and the request reaches the backend through CloudFront and the ALB.
2. The backend creates payment parameters and a signature for the payment provider.
3. The private Fargate task calls the provider through the NAT Gateway.
4. The provider returns a payment URL, which the backend returns to the browser.
5. After payment, the backend validates the provider signature and status before marking an order as paid or confirmed. A client redirect or callback is not trusted by itself.

#### Validation scenarios

1. The React SPA loads from S3 through CloudFront.
2. A request to `/api/*` reaches the ALB, healthy Fargate targets, and an RDS-backed response.
3. Fargate has no public IP but remains reachable through the ALB and has outbound access through NAT.
4. RDS is private and accepts port `5432` only from `ecs-sg`.
5. Concurrent checkout does not oversell inventory through Optimistic Locking.
6. Payment URL creation and sandbox status verification complete successfully.
7. A new ECR image and task-definition revision redeploy successfully.
8. A misconfigured database endpoint or health-check path can be diagnosed through ECS stopped reasons, ALB target health, and CloudWatch Logs.

#### Cost review

Review NAT Gateway runtime and data processing, ALB/Fargate/RDS duration, Cross-AZ traffic paths, and the time range and query scope used in CloudWatch Logs Insights. Use the RDS backup and snapshot capability when enabled; this architecture does not use the former EC2-to-S3 backup flow.

Monitoring, validation, and cost review are part of the deployment work. A running workload can still have a broken payment flow, incorrect inventory behavior, or unnecessary cost.
