---
title: "TIL: Notes on using a MongoDB Atlas free shared instance for development"
date: 2022-01-12
tags:
  - til
  - mongodb
  - devops
layout: layouts/post.njk
---

When deploying [mdbssg](github.com/tydar/mdbssg) to Heroku, I needed a cloud-hosted Mongo instance to act as the backing DB. Heroku does offer several hosted DB options, but no Mongo add-on has a free tier. However [MongoDB Atlas](https://www.mongodb.com/atlas/database) does have a free shared tier.

Once you sign up for a MongoDB Atlas account, you can create a free shared cluster on AWS, GCP, or Azure. During configuration, you can choose username and password or certificate-based authentication. You also can configure network rules for access to the database. Paid tiers have a few different options, but the free tier only allows using an IP list.

This causes a problem for backing a free Heroku app with MongoDB. Heroku Dynos are recreated at least daily. They do not have a static IP by default, and their possible IP range is broad--the entirety of an AWS region. So you have options:

1) Use the free tier of a static IP outbound proxying service like Fixie Socks--however this only provides a very limited number of outbound requests, in the order of 100 per day. This is alright for a simple demo app, but you could imagine hitting that limit easily if you are heavily testing a feature that hits your DB.
2) Open up the MongoDB instance to any IP (using a 0.0.0.0/0 rule).
3) Determine the IP range for your Heroku app [by AWS region](https://help.heroku.com/JS13Y78I/i-need-to-add-heroku-dynos-to-our-allowlist-what-are-ip-address-ranges-in-use-at-heroku) and add that as an allowed IP range in Mongo Atlas.

I chose to do rule (2) with a couple caveats. First, I would use the Atlas feature to time-limit access based on the new 0.0.0.0/0 rule. My rule would automatically expire in 6 hours even if I took no action. Second, I would always delete the rule when I finished working on the app. This means the app is broken unless I add my rule again--since I only need it to work when I am actively developing and since I did this project primarily for experience with the DB, Go language bindings, and cloud providers, it does not need to work except when I want it to.
