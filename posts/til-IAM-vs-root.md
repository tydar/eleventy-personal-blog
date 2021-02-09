---
title: "TIL: IAM vs root account"
date: 2021-02-09
tags:
    - til
    - aws
    - devops
layout: layouts/post.njk
---
The AWS console allows you to interact with a huge amount of AWS services. It also allows you to shoot yourself in the foot if you're not careful. A very basic restriction to provide some protection against truly devestating foot-shooting is *using an IAM account for day-to-day tasks*.

Using a root account in AWS console gives you permissions beyond what is needed for most any administrative task. Instead, you should create an IAM account with AdministrativeAccess for most activities. To do that, you first open the [AWS IAM console](https://console.aws.amazon.com/iam/).

Then, open the *User* section in the navigation menu followed by choosing *Add user*. Then you'll fill in a username, set up programmatic or management console access as needed. Follow your organizational policy for password creation & reset on first login.

Next, on the permissions page, you'll select the default *Add user to group* and *Create group* if needed. Ensure to check the entry *AdministratorAccess* in the policy list. Add tags to the user if you'd like, then review and create.

Now you've got an IAM account that will keep your hands a little further from The Button when working on day-to-day AWS tasks.
