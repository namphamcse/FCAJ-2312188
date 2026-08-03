---
title: "Create the S3 Bucket and Upload the Frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Create the S3 bucket

1. Start by installing dependencies and building the frontend. For this React/Vite application, run `npm install`, then `npm run build`.
2. Open the Amazon S3 Console and create a bucket with a unique name.
3. Keep Block Public Access enabled so no one can reach the bucket directly from the Internet.
4. Upload the newly generated `dist` directory, including `index.html`, CSS, JavaScript, and static assets.

The bucket is the CloudFront origin; users do not access S3 objects directly.

![S3 bucket containing the frontend](/FCAJ-2312188/images/5-Workshop/s3_bucket.jpg?featherlight=false)
