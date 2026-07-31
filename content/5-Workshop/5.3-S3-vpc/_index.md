---
title: "Deploy the Frontend with S3 and CloudFront"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Overview

The frontend is a static website built into HTML, CSS, and JavaScript. These files are uploaded to a private S3 bucket. CloudFront uses the bucket as an origin to deliver content to users, cache it at edge locations, and avoid a public S3 bucket.

#### Contents

1. [Create the S3 bucket and upload the frontend](5.3.1-create-gwe/)
2. [Create the CloudFront distribution](5.3.2-test-gwe/)
