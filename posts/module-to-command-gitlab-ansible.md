---
title: "Troubleshooting a configuration problem"
date: 2021-01-11
tags:
    - ansible
    - gitlab
layout: layouts/post.njk
---
After I [wrote an Ansible role](/posts/convert-playbook-to-role/) to better modularize my GitLab runner configuration, I started some new Debian VMs [using FAI](/posts/til-fai/) to test the role in the production pipeline for my KVM Conjurer project. I realized quickly that my GitLab controller was not communicating with the runners. Runners would show up in the GUI but would not take jobs or have further communication with the controller.

Very strange. It took me a couple of tries poking around to figure out what exactly was going on.

My very first assumption was that something I'd done in setting up the servers was causing the error. I deleted the VMs, spun up two fresh instances, and ran my playbook again. But again the runners were not communicating with the controller.

Next, I opened up `/etc/gitlab-runner/config.toml` on one of my runner boxes. The entire content was:

```
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800
executor = shell
```

Strange! No [[runners]] section and the executor line standing on its own. I thought I might have had a major misunderstanding of the regex used to ensure the executor was set to shell (a hacky solution I was unhappy with). I commented out that section of my playbook and started again from scratch. This time, a `config.toml` similarly sparse:

```
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800
```

Stranger still. No Ansible task that might be stomping on the rest of the configuration file. I consider copying a known-good configuration file after running the registration command. So I tried manually registering the runner with the `gitlab-runner` CLI utility. And I got a working `config.toml` and a runner that would take jobs!

But the registration command gets each runner a unique token. So just copying a good configuration file is out of the question. A bit of Google searching later and I see a few Ansible roles online that invoke the `gitlab-runner register` CLI command instead of using the community module like I had been. I modify my task to do that instead.

And it works! But I'm still unsure why the module is broken. I know that it is using an earlier version of the gitlab-python library, so my best guess is that the library version it is using doesn't know how to register with a more recent GitLab controller. An updated module would be nice if this is the case. But a role making use of the CLI tooling works perfectly for now.

Overall this process was a good proving of the basic troubleshooting methodology: identify where it breaks, make a hypothesis, and test that hypothesis. When your hypothesis isn't quite write, test a slightly different way to identify where the problem lies.
