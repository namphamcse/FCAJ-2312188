---
title: "Create the S3 Bucket and Upload the Frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Create the S3 bucket

1. Install dependencies and build the frontend. For this React/Vite application, run `npm install` and `npm run build`.
2. Open the Amazon S3 Console and create a uniquely named bucket.
3. Keep Block Public Access enabled so that the bucket is not directly reachable from the Internet.
4. Upload the generated `dist` output, including `index.html`, CSS, JavaScript, and static assets.

The bucket is the CloudFront origin; users do not access S3 objects directly.

![S3 bucket containing the frontend](/FCAJ-2312188/images/5-Workshop/s3_bucket.jpg?featherlight=false)
