---
title: Using Ansible to configure fresh Debian machines
description: Using basic Ansible playbooks to set up essential utilities on new servers
date: 2021-01-01
tags:
  - ansible
  - infrastructure-as-code
  - gitlab
  - linux
layout: layouts/post.njk
---
I've been tinkering with a self-hosted Gitlab instance to learn a bit more about their CI/CD tooling and to practice with a vertically integrated set of tools overall. I set up my controller and runner on Debian 10 virtual machines running on an ESXI host. I first leaned toward using the Docker containers available to spin up and deploy Gitlab runners--but the main software project I'm working on is a webapp acting as a front-end for libvirt called [KVM Conjurer](https://github.com/tydar/kvm-conjurer).

It seems to me like more trouble than it is worth to get a containerized libvirt deployment set up--at least for now. Instead I decided to use the shell Gitlab executor. My main concern was reproduceability of working test and eployment machines. So I quickly put together [some Ansible playbooks](https://github.com/tydar/ansible-gitlab-scripts) to take a fresh new Debian 10 install and bring it up to the baseline necessary to build and run KVM Conjurer *and* to host a few minimal VMs locally to enable automated testing as part of the CI pipeline provided and configured in Gitlab.

Here are the details of [the first playbook](https://github.com/tydar/ansible-gitlab-scripts/blob/main/basic-deb10-setup.yml):

```
- name: install sudo, vim, .vimrc, add my user to sudoers, and install open-vm-tools
  remote_user: root
  hosts: all

  tasks:
      - name: install sudo
        apt:
            name: sudo
            update_cache: yes

      - name: add tyler to sudoers
        user:
            name: tyler
            groups: sudo
            append: yes

. . . 
```
This file does a few things to make a new minimal Debian install more liveable. First, it installs sudo and adds the non-root user I create during installation to the sudoers usergroup. This uses the `ansible.builtin.apt` and `ansible.builtin.user` modules to make that configuration.

From here, I install several more utilities (vim, git, etc). I've elided that to show two more Ansible modules in use.
```
. . .
      - name: pull vimrc from git
        become: yes
        become_user: tyler
        git:
            dest: /home/tyler/.vim_runtime
            repo: 'https://github.com/amix/vimrc.git'
            depth: 1

      - name: install basic vimrc
        become: yes
        become_user: tyler
        copy:
            src: /home/tyler/.vim_runtime/vimrcs/basic.vim
            dest: /home/tyler/.vimrc

``` 
This snippet shows the use of the `ansible.builtin.git` and `ansible.bulitin.copy` utlities. Since I don't have much of a custom `.vimrc` put together for myself, I grab and install a basic `.vimrc` [from Todoist founder Amir Salihefendic](https://github.com/amix/vimrc).

I'll shortly write up my next two playbooks: one to prepare a machine to act as a Gitlab runner and one that prepares a machine to build and run KVM Conjurer.
