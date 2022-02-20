---
title: "TIL: Open a port in the NixOS firewall"
date: 2022-02-19
tags:
  - til
  - nixos
  - linux
  - networking

layout: layouts/post.njk
---

While running through [this tutorial](https://nixos.org/guides/dev-environment.html) for a minimal application running on NixOS, I ran into an issue. My test NixOS install is on an ESXi box and I wanted to access my test app from my desktop. The NixOS firewall does not allow external TCP traffic on the default Flask test port 5000.

To open a port on the NixOS firewall, I took these steps:

1) Open `/etc/nix/configuration.nix` in a text editor per [this documentation](https://nixos.org/manual/nixos/stable/index.html#sec-changing-config).
2) Add the following line of Nix to the configuration per [this documentation](https://nixos.org/manual/nixos/stable/index.html#sec-firewall):

```nix
networking.firewall.allowedTCPPorts = [ 22 5000 ];
```

for a final `configuration.nix` of

```nix
{ config, pkgs, ...  }:

{
  imports = [ <nixpkgs/nixos/modules/installer/cd-dvd/installation-cd-minimal.nix>  ];
  networking.firewall.allowedTCPPorts = [ 22 5000  ];   
}
```

3) Run `# nix-rebuild test` to ensure that I haven't broken SSH access.

4) Follow the original tutorial steps to run my test Flask app. Then open `<local_ip>:5000` in my desktop browser.

5) If I want to keep this test port open permanently, I can now run `# nix-rebuild switch`.
