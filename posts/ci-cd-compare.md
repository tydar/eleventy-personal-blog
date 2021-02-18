---
title: "Comparing common (free) continuous integration / continuous deployment tools"
date: 2021-02-18
tags:
    - CI
    - devops
layout: layouts/post.njk
---
Making a decision between different available CI/CD tools can be difficult. Many of the available platforms are relatively comparable. Pricing structures also differ heavily between available tools. Here's a quick rundown of some factors you should consider--and some brief exploration of the differences between free tiers for some common CI/CD providers.

First, you need to decide whether you'll use a hosted version control repo on a platform like GitHub, GitLab, or AWS CodeCommit or a self-hosted git repo. Most (if not all) CI/CD platforms will support any git repos. Some platforms' free tier, like GitLab, will require you to use a GitLab instance (either gitlab.com or self-hosted) as the main repo.

Second, the pricing for CI/CD tools will differ for open- and closed-source projects. GitHub Actions, for example, is totally free for all public repos. TravisCI provides some free services for verified open-source projects.

Next, you'll want to determine whether you'll use service-provided runners or host your own build/run machines. Most provide some hosted build-minutes on their free tier. Some services allow self-hosted runners for free (such as GitLab). Others require a particular tier of subscription (like CircleCI). If you self-host runners, that will be an additional burden on you or your team.

Depending on the language and toolchain for your build, there may be relevant differences between CI/CD services. More popular services will be more likely to have openly available tutorials for configuring more advanced build systems. Some tools, like Jenkins, might require more effort to fully configure than a hosted solution.

Next, you'll want to consider the deployment options for your project. If you're planning to keep your test and production infrastructure in AWS or Azure, you might consider using their CI/CD tools to keep all of your infrastructure in one system. On the other hand, you might prefer to keep your build system outside those environments to avoid the possiblity of a broad failure in that infrastructure inhibiting your ability to deploy to another cloud or cloud region.

Finally, you should consider your own experience with CI/CD tools. The best tool for your use case is the one you'll use and the one you can get working reliably. Unless you're deliberately choosing to learn a new tool or have a compelling technical reason to switch, familiarity will keep you more productive.

So, let's compare some popular CI/CD services. Of course, there are more tools than listed here. I chose to compare these based on the needs for a friend's side project.

* GitHub Actions
    * Totally free service for public repos.
    * Provides a free tier for private repos: 2,000 minutes per month.
    * For additional build minutes on a basic Linux host, the price is $0.008/minute. There are also subscription plans including additional features and large pools of minutes.
    * Allows free use of self-hosted runners.
* GitLab
    * Comprehensive DevOps platform: version control, issue tracking, CI/CD, and more.
    * 400 build-minutes free on gitlab.com hosted service.
    * Free tier supports self-hosting of all features.
    * With free tier, GitLab repo must be the master repo. Can only mirror pushes to GitHub.
    * Cheapest premium plan is $19/user/mo includes 10,000 CI/CD minutes on hosted platform.
    * Feature-rich but may be overkill if all you need is CI/CD.
* Azure DevOps
    * Comprehensive platform, like GitLab.
    * Free tier includes one parallel build job with 1,800 runner minutes per month.
    * $40/mo/parallel job for unlimited build minutes
* CircleCI
    * 2,500 free credits/week for free tier.
    * One parallel job on free tier.
    * Credit price per minute varies depending on features of build machine.
    * First paid plan is $30/mo with up to 80 concurrent jobs.
    * Self-hosted runners require subscription.
* TravisCI
    * Free tier is a trial. You receive 10,000 credits that are gone when they're gone. 
    * $69/mo for one concurrent job.
    * Some free service for open-source projects. Requires talking to TravisCI team.
* Jenkins
    * Open-source and typically self-hosted.
    * Stable solution used in production longer than some new services.
    * Requires extensive configuration and infrastructure support.
* AWS CodePipeline/CodeBuild
    * 100 free build minutes per month on CodeBuild.
    * 1 free active pipeline per month on CodePipeline.
    * Additional build minutes on small Linux instance are $0.005/minute in us-east-ohio region.
