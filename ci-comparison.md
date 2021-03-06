---
title: "Comparison of CI/CD solutions"
date: 2021-01-04

permalink: /cicomparison/

layout: layouts/post.njk
---

# CI/CD Services

Comparison of some popular CI/CD services. The services I'll be comparing:

* GitLab
* GitHub Actions
* Jenkins
* Azure DevOps
* AWS CodePipeline
* TravisCI
* CircleCI

## Gitlab

Gitlab provides both hosted and self-host options. It is not just a CI/CD pipelining service. It provides a top-to-bottom solution from repository hosting, automated build, test, and deployment. There is a free tier for both hosted and self-hosted Gitlab:

Here are the features available on the free tier:
* 400 pipeline minutes (Gitlab.com hosted)
* Artifact management
* Issue boards
* Wiki documentation for projects
* Design management (for wireframes etc)
* BYO Runners (e.g. run a VM so you don't use online VM minutes)
* BYO production environment (deploy to any platform)
* Many integrations with testing suites, deployment platforms, etc.
* Can run builds in Docker containers.

Confiuration provide in `.yml` files. 

One large potential drawback: mirrored repo services are limited in free tier. You can only push Gitlab to GitHub, can't pull back.

## GitHub Actions

GitHub Actions is built-in to GitHub, so it allows you to work with a GitHub repo natively without needing to set up separate authentication. I think I have read that GitHub Actions are somewhat poorly supported and can falter on larger projects. A one-person dev project may not be concerned about that.

Pricing for Github Actions is:
* Totally free for public repos. Open source projects are supported.
* Free tier for private is 2,000 minutes per month. Additional minutes without purchasing a whole new tier are $0.008 per minute for a Linux machine.
* Can also self-host runners for free.
* Supports deployment to many platforms, automated issue tagging and generation, testing, code coverage, etc.

Configured with `.yml` files.

## Jenkins

Jenkins is an open-source automation server. Can do much more than CI/CD, but has robust support for CI/CD pipelines as a core use case. It is written in Java and is very popular with larger enterprise users.

Jenkins is totally free for a self-hosted solution.
* Integration with many version control systems and many online version control platforms.
* Many plugins for different features, build systems, other integrations.
* Can use Docker containers to run builds and other jobs.

Jenkins is a bit more resource hungry than other products in my brief experience, due to Java.

Jenkins allows configuration of pipelines with a Declarative DSL and with a subset of Groovy.

## Azure DevOps

Azure DevOps includes many services. Most important for CI/CD directly are Azure Pipelines and Azure Artifacts. There is an Azure Pipelines appication for GitHub that allows free integration.

The free tier includes:
* 1 Microsoft-hosted job with 1,800 minutes per month for CI/CD.
* 1 self-hosted job with unlimited minutes per month.
* 2 GiB of artifact storage for free.

`.yml` configuration.

## AWS CodePipelines

AWS CodePipelines is the AWS-hosted CI/CD service. Also provides free connection to GitHub repo.

Free tier:
* One free Active Pipeline per month.
* 100 free minutes from CodeBuild per month.
* CodeDeploy is a free service to deploy to EC2 or Lambda.

## TravisCI

TravisCI's free tier is a trial--you are given 10,000 credits. When you use them up you must start to pay. Has similar features to many other platforms. Integrations with all relevant platforms. I didn't get a clear answer immediately to how many build-minutes 10,000 credits is.

## CircleCI

Seems like a popular option now. Haven't looked into it much yet.

Free cloud tier:
* 2,500 free credits/week
* Build on Linux and Windows


## Note on self-hosting

Self-hosting of any provider that supports it can be done on the cloud. So for sufficiently resource-friendly builds a free-tier EC2 instance may be useable. Can also likely set up a $5/mo Digital Ocean droplet that can handle it all for small projects.
