---
title: Converting Ansible plays to Ansible roles
description: Ansible roles are a more easily reusable, configurable, and composable way to package Ansible configuration scripts.
date: 2021-01-07
tags:
    - devops
    - ansible

layout: layouts/post.njk
---
## Why refactor to roles?

In an earlier post, I discussed [a set of Ansible playbooks](https://github.com/tydar/ansible-gitlab-scripts) I'd quickly written to configure my Debian 10 GitLab runners for use in the CI/CD pipeline for my [KVM Conjurer](https://github.com/tydar/kvm-conjurer) project.

I quickly realized these playbooks are a bit clunky to maintain. While I could develop some conventions for myself about where to store secrets and how to import them, relative pathing for files I need to copy, and so on, I could see a set of so-called "conventions" becoming a long list of one-off rules I'd created in the moment that I needed to make a configuration change.

Brief investigation brought me to the concept of [Ansible Roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html). Ansible Roles are essentially a standard way to organize a set of Ansible configuration scripts and their dependencies for ease of reuse, understanding and patching, and sharing. The final product of this process is [hosted on GitHub](https://github.com/tydar/gitlab-ansible-role).

## Basic overview

The final filesystem structure for the three roles replacing my individual configuration playbooks looks like this:

```

/home/tyler/ansible-playbooks/homelab-ansible-scripts/
|-- inventories
|   `-- gitlab_runners.yml
|-- plays
|-- roles
|   |-- conjurer_build
|   |   `-- tasks
|   |       `-- main.yml
|   |-- debian_utils
|   |   |-- defaults
|   |   |   `-- main.yml
|   |   `-- tasks
|   |       `-- main.yml
|   `-- gitlab_runner
|       |-- defaults
|       |   `-- main.yml
|       |-- files
|       |   `-- pin-gitlab-runner.pref
|       `-- tasks
|           `-- main.yml
|-- secrets
|   |-- keys.yml
|   `-- vars.yml
`-- update-all.yml

13 directories, 11 files
```

A full Ansible role can include many more subdirectories that provide different features. Here I used tasks, default variables, and files.

You can view these files [on GitHub](https://github.com/tydar/gitlab-ansible-role). Note that I've excluded the directory `secrets/` and the directory `inventories/` from my publicly committed role.

## Tasks

The tasks folder contains files describing the tasks that might be run by the role. Ansible starts by looking for `main.yml` for the place to start execution. You can store and include other lists of tasks here. For example, you might have separate files describing the configuration on different OSes.

To convert from a playbook to a role, this was a very simple step: just copy the contents of the `tasks:` section of your playbook. So, my basic `conjurer_build/tasks/main.yml` file is a cleaned-up version of my `configure-conjurer-build.yml` playbook.

## Vars

The vars folder and the default vars folder are used to store files providing variables or default variable values for your tasks. Here I've only chosen to provide default variables for my core `debian_utils` installation script. Variables in `vars/` are preferred over those provided in `defaults/`.

In my new `debian_utils/defaults/vars.yml` I have a default user list of one and a default value of false for two vars indicating to treat the Debian machine as bare metal unless specified to be running in a particular virtualization environment.

```
---
users_list:
    - tyler

kvm: false
esxi: false
```

## Files

The `files/` directory is intended to hold files you may need to copy to the remote host as part of the role being ran. Ansible provides files stored in this directory to your tasks without any need for prefixing or pathing. A file stored at `files/example.conf` can be referenced as `example.conf` in a task.

To configure a machine as a GitLab runner, this role needs to copy a file. This file is placed in `gitlab_runner/files/` and is accessed simply in that role's tasks as `pin-gitlab-runner.pref` with no pathing or prefix necessary:

```
- name: copy APT pinning file to ensure gitlab repo used
  copy:
    src: pin-gitlab-runner.pref
    dest: /etc/apt/preferences.d/pin-gitlab-runner.pref
```

## Using the new script

To use these new roles, we create a playbook in the root directory next to the `roles` file (you can also place the roles in `/etc` somewhere to make them available to any Ansible play on your machine. I prefer to keep things out of global scope if possible, to avoid causing myself hard to diagnose problems later).

Here's an example playbook using my `debian_utils` role and passing in a set of users to create:

```
---
- hosts: runners
  roles:
    - role: debian_utils
      vars:
        users_list:
            - tyler
            - generic_admin
```

Given an inventory with hosts in a group called "runners" this playbook will run the tasks defined in `debian_utils/tasks/main.yml` on each machine, adding users "tyler" and "generic_admin".

Similarly, you can set up a fresh Debian 10 image from start to a GitLab runner capable of building KVM Conjurer by using a playbook like this:

```
---
- hosts: runners
  roles:
    - debian_utils
    - conjurer_build
    - gitlab_runner
```

All hosts in the group "runners" will have these roles applied in order with default settings.

## What next?

Currently these roles do not provide any handlers. It would be useful to me to have basic CRUD handlers for runners available. I typically only need one runner when I'm actively working. I might want to temporarily deprovision all runners to free resources on my hypervisor hosts for another lab project. Handlers could facilitate that.

I'd also like an Ansible role that allows me to deploy a fresh Debian or other Linux machine from a cloud image and cloud-init configuration. That would give me greater flexibility in automated deployment of runners.

Finally I'd like to have the flexibility to deploy runners on a Linux distribution other than Debian. In particular I'm investigating Fedora and openSUSE for this purpose.
