---
title: "TIL: CloudFront redirect issues with newly created S3 buckets"
date: 2021-03-13
tags:
    - til
    - aws
    - devops
layout: layouts/post.njk
---
Today, I was attempting to [host static content on S3 with access provided through CloudFront](https://www.wellarchitectedlabs.com/security/100_labs/100_cloudfront_with_s3_bucket_origin/). This configuration restricts access to objects in the S3 bucket--the content can only be accessed through the designated CloudFront distribution endpoint. This setup only requires a few clicks in the AWS S3 and CloudFront management consoles.

However, I kept receiving an AccessDenied error when visiting my CloudFront URL. After destroying and recreating the bucket and distribution just a few times to ensure I was properly executing the process, I discovered my issue. By default, CloudFront was configured to use the global S3 URL for my bucket. Since the bucket was relatively new, the CloudFront distrubtion was forced by incomplete DNS propogation to redirect to the S3 bucket URL itself.

The problem has a one-step fix: change the global S3 URL (of the format `bucket-name-here.s3.amazonaws.com`) to the region-specific format (`bucket-name-here.s3.region.amazonaws.com`). Almost immediately, the issue was resolved.

Solution found [here](https://stackoverflow.com/questions/38735306/aws-cloudfront-redirecting-to-s3-bucket).
