---
title: "Create the S3 Bucket and Upload the Frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Create the S3 bucket

1. Open the Amazon S3 Console and create a uniquely named bucket.
2. Keep Block Public Access enabled so that the bucket is not directly reachable from the Internet.
3. Upload the frontend build output, including `index.html`, CSS, JavaScript, and static assets.

The bucket is the CloudFront origin; users do not access S3 objects directly.

![S3 bucket containing the frontend](/FCAJ-2312188/images/5-Workshop/s3_bucket.jpg?featherlight=false)
