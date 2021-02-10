---
title: "TIL: Use AWS CloudWatch to monitor workload health"
date: 2021-02-10
tags:
    - til
    - aws
    - devops
layout: layouts/post.njk
---
AWS CloudWatch makes it straightforward to trigger events based on application, workload, or infrastructure metrics. Based on the project described by the [Amazon WA Lab for dependency monitoring](https://www.wellarchitectedlabs.com/operational-excellence/100_labs/100_dependency_monitoring/) an easy example is to evaluate the invocation of a Lambda function.

First, establish a baseline for how frequently the function should be invoked (in the lab, once per minute base on the SLA with an external dependency).

Second, visit the CloudWatch console, choose the Alarms pane in the navigation bar to the left, then add an alarm. Find the appropriate Lambda function using the search, choose invocations, and then use the provided controls to tune the level of invocations that will trigger an alert. In the lab, we trigger the alert when receiving less than 1 invocation per minute.

Through the Alarm wizard, you can connect the alarm to an SNS topic.

Once the alarm is created and tied to an SNS topic, you can subscribe other AWS services to that topic. Then, based on the alarm being triggered, you can tie in an autoscaling event, a restart or reconfiguration of a service, or another action to attempt an automatic remediation.
