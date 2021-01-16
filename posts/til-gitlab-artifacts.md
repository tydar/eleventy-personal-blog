---
title: "TIL: manage build artifacts with Gitlab CI"
description: use your Gitlab configuration files to specify which build artifacts to preserve
date: 2021-01-04
tags:
   - devops
   - gitlab
   - til

layout: layouts/post.njk
---
When you run a job in Gitlab, you can specify artifacts to preserve in your `.gitlab-ci.yml` file. Here's the simple build job for KVM Conjurer:

``` yml
build-job:
  tags:
      - debian
  stage: build
  script:
      - poetry build
  artifacts:
      paths:
          - dist/*.whl
```

The `artifacts:` directive allows you to define a path to files (including pattern matching) that should be preserved and accessible in your Gitlab instance. Here I've chosen to keep the `.whl` files to allow easy access to the build for myself and to use the `.whl` file in the deployment step later.

More information on how to manage artifacts in Gitlab CI/CD [in the official docs](https://docs.gitlab.com/ee/ci/pipelines/job_artifacts.html).
