---
title: "Create the CloudFront Distribution"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Configure CloudFront

1. Create a CloudFront distribution and select the S3 bucket as its origin.
2. Use Origin Access Control so CloudFront can read objects from the private S3 bucket.
3. Set the default root object to `index.html`.
4. Add the ALB as a second origin and create a `/api/*` behavior that forwards the required API methods, headers, and query strings.
5. Configure SPA fallback so client-side routes return `index.html` when appropriate.
6. When the distribution is deployed, open the frontend through the CloudFront domain name.

#### Deployed distribution settings

| Setting | Value |
|---|---|
| Distribution | `fe cloudfront` |
| Public domain | `d2m34udjfc5fxq.cloudfront.net` |
| Default origin | Private S3 bucket containing the React/Vite `dist` artifacts |
| API origin | Internet-facing `shopsflow-alb` |
| API behavior | `/api/*` forwarded to the ALB |
| Default root object | `index.html` |

When the frontend changes, create an invalidation for files that need to be released again.

![CloudFront frontend distribution](/FCAJ-2312188/images/5-Workshop/fe_cloudfront.jpg?featherlight=false)
