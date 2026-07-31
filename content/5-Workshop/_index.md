---
title: "Workshop"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

This workshop deploys a cloud-native web application on AWS. The frontend is stored in Amazon S3 and delivered through Amazon CloudFront. The containerized backend runs on Amazon ECS Fargate behind an Application Load Balancer (ALB), with Amazon RDS for PostgreSQL as the database.

### Contents

1. [Architecture overview](5.1-Workshop-overview/)
2. [Prepare the VPC and security groups](5.2-Prerequiste/)
3. [Deploy the frontend with S3 and CloudFront](5.3-S3-vpc/)
4. [Deploy the backend with ALB, ECS Fargate, and RDS PostgreSQL](5.4-S3-onprem/)
