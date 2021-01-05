---
title: Creating QEMU guests from cloud images
description: Use libvirt-powered tools to create QEMU guests from cloud image format with cloud-init configuration
date: 2021-01-01
tags:
  - cloud-init
  - linux
layout: layouts/post.njk
---

# Setting up a test environment for KVM Conjurer

This documents the simple scripts I developed to enable easy initial testing of certain views in my KVM Conjurer project. It relies on existing libvirt-powered tools to get the qemu:// hypervisor to a certain state.

## Step 1: Obtain (or detect cached) .qcow2 base image

Grab a cloud image from the Debian mirrors if we don't have it on disk already:

```
test -f images/debian10.qcow2 || wget -O images/debian10.qcow2 https://cloud.debian.org/images/cloud/buster/20201214-484/debian-10-generic-amd64-20201214-484.qcow2
```

## Step 2: Create .qcow2 image with base image as backing image

In my current test environment script, I create 2 virtual machines, so I need two of these overlay images.

qemu-img create -f qcow2 -F qcow2 -o backing_file=images/debian10.qcow2 test1.qcow2
qemu-img create -f qcow2 -F qcow2 -o backing_file=images/debian10.qcow2 test2.qcow2

## Step 3: Create ISO with cloud-init configuration

There are two sub-steps here. First, create the configuration files. Second, package them to be read by the virtual CD-ROM drive.

### Substep 1: Create cloud-init.cfg

You can see the file I created on GitHub here. Full cloud-init documentation can be found on read-the-docs.


### Optional network configuration

I opted to not include a network configuration in my scripts, since I do not need network configuration for my current test suite. You can find more information about configuring networks in the cloud-init documentation [^3] or at Fabian Lee's blog [^2].

### Substep 2: run cloud-localds to produce .img

Again, I need 2 cloud-init seed images because I am creating two virtual machines for testing. This tool is provided by the package `cloud-image-utils` (the name may vary; I am using Debian 10).

```
cloud-localds -v test-seed1.img cloud-init.cfg
cloud-localds -v test-seed2.img cloud-init.cfg
```

## Step 4: Boot using virt-install

We boot from the virtual overlay harddrive using the `--boot hd` option, provide the cloud-init configuration through the `--disk path=test-seed1.img,device=cdrom` option, and avoid being tossed to an interactive console session with `--noautoconsole`. The other options are standard or discretionary depending on your context.

```
virt-install --name test1 \
    --memory 1000 --vcpus 1 \
    --boot hd,menu=on \
    --disk path=test-seed1.img,device=cdrom \
    --disk path=test1.qcow2,device=disk \
    --graphics none \
    --noautoconsole \
    --network network:default \
    --os-type=linux --os-variant=debian10
```

This command is ran twice modulus the seed and overlay image filenames.

## Sources

[^1]: [Install cloud guest with virt-install and cloud-init configuration](https://quantum-integration.org/posts/install-cloud-guest-with-virt-install-and-cloud-init-configuration.html)

[^2]: [KVM: Testing cloud-init locally using KVM for an Ubuntu cloud image](https://fabianlee.org/2020/02/23/kvm-testing-cloud-init-locally-using-kvm-for-an-ubuntu-cloud-image/)

[^3]: [cloud-init documentation](https://cloudinit.readthedocs.io/en/latest/)

[^4]: [libvirt knowledgebase: Backing chains](https://libvirt.org/kbase/backing_chains.html)
