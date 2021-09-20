---
title: "Use a Raspberry Pi Zero W as a print server"
date: 2021-09-19
tags:
  - pi
  - linux

layout: layouts/post.njk
---
I've had a Raspberry Pi Zero W since last Christmas--it was a great gift from my in-laws. I asked for it so I could turn my old reliable HP LaserJet 1020 black-and-white printer into a networked printer without having to run the server on my desktop or have its location tied to my larger homelab machine.

It took about 9 months, but I finally finished setting it up today. I'll give the broad steps for setup and a few learnings.

## Setting up a Raspberry Pi

1) Install your OS of choice.
2) Ensure you have an account in the group `lpadmin` whose password you know.
3) Install hplib and cups (I just used the version in the Debian repos).
4) Run `hp-plugin -i` as root or using `sudo`. This took a while on the Pi Zero.
5) Optionally run `cupsctl --remote-admin`. I read some documentation that stated machines on the same subnet should not require remote access to be allowed. I think this may only apply to the print queues, not the admin panel by default.
6) Open the cups web interface at `https://<IP_OR_HOSTNAME_FOR_PI>:631`. Add your printer, safely choosing the HP LaserJet 1020 drivers, having installed the proprietary drivers with `hp-plugin`.

## Miscellaneous notes

1) For a Pi Zero, the standard full Raspbian install with a DE is too heavy. Stick to Raspbian Lite or another headless-first distro.
2) Use a power supply with sufficient amperage. I initially used a 1 amp PSU I had lying around and couldn't get my keyboard to power up.
3) A 1GHz single-core processor is fast enough for more than you'd expect, but it's still slow.
4) A lot of existing packages are already available for armhf machines. I sort of expected legacy printer drivers to be a hassle but they were just available, at least for my hardware.