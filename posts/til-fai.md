---
title: "TIL: deploy Debian 10 automatically with FAI"
date: 2021-01-11
tags:
    - linux
    - til
layout: layouts/post.njk
---
Second TIL of the day, but a quick one. I run most of my development and CI VMs currently on an ESXI host. As a stopgap while I set up additional hosts using KVM and QEMU to manage VMs (which very easily support cloud-init), I discovered FAI, a project that provides fully automatic install images for Debian. While they are less configurable without a dedicated FAI-server, they still provide much quicker install of clean Debian for testing than a standard install.

To use FAI, download an image from the [FAI download page](https://fai-project.org/fai-cd/) and boot the VM from the image. Choose your OS in GRUB and continue. You can also generate an image using [the FAI.me tool](https://fai-project.org/FAIme/).
