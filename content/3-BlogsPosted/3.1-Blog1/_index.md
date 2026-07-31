---
title: "S3, IMDSv2, and Lambda: three AWS operational issues to avoid"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

Instead of discussing broad concepts, this article highlights three small AWS details. They rarely appear on lecture slides, yet they can create unnecessary costs or lead to days of debugging.

### 1. “Invisible” S3 files: you still pay, but cannot see them

When an application uploads a large file to S3 and the connection drops midway, the multipart upload does not complete.

**The overlooked detail:** Data parts that were transferred before the failure remain stored in S3 and continue to incur storage charges. These incomplete parts do not appear when viewing the bucket in the S3 Console or when using the standard `aws s3 ls` command. If many large uploads fail, the hidden cost can become significant.

**How to handle it:** Create an S3 Lifecycle Rule that deletes incomplete multipart uploads after 1–2 days. This automatically removes data parts that are no longer needed.

### 2. The IMDSv2 hop-limit trap for containerized applications

Moving from IMDSv1 to IMDSv2 is an important security practice that reduces the risk of exposing IAM role credentials on EC2. However, an application running in a Docker container on an EC2 instance can lose access to the EC2 Instance Metadata Service, resulting in authentication errors from the AWS SDK.

**Why it happens:** IMDSv2 controls the hop limit of metadata responses. A request from a container can cross a Docker network bridge or virtual interface before reaching the metadata address, `169.254.169.254`. If the EC2 instance's `HttpPutResponseHopLimit` is only `1`, the response may not be able to return to the application in the container.

**How to handle it:** For a containerized workload that requires instance metadata, increase the EC2 instance's Metadata Response Hop Limit from `1` to `2`, while granting only the IAM permissions required by the instance role.

### 3. The AWS Lambda `/tmp` directory is not always clean

It is easy to assume that every Lambda invocation runs in a new environment. In reality, AWS can reuse an execution environment for later invocations through warm starts. A file written to `/tmp` during one invocation can therefore remain for the next one.

**Consequences:**

* **Security risk:** Temporary files that contain sensitive data should be removed after processing so they are not used incorrectly by later requests.
* **Disk-full risk:** Accumulated temporary files can exhaust ephemeral storage and cause unpredictable `No space left on device` errors.

**How to handle it:** Remove temporary files proactively after processing, for example with a `try...finally` block. Do not depend on Lambda creating a new execution environment.

These details are small, but they can directly affect cost, security, and system reliability. Checking them early helps keep systems running smoothly and avoids unnecessary hidden incidents.

### Blog Link

[View the blog post on Facebook](https://www.facebook.com/share/p/1BVqPJMVwu/)
