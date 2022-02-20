---
title: "TIL: Build a Go app from local src directory on NixOS"
date: 2022-02-20
tags:
  - til
  - nixos
  - linux

layout: layouts/post.njk
---

To run a simple build of a Go project on NixOS, you can write a small `default.nix` file into the root directory of your project. Here is my `default.nix` for [Stomper](https://github.com/tydar/stomper).

```nix
with import <nixpkgs> {};

buildGoModule {
        pname = "stomper";
        version = "0.0.1";

        src = ./.;

        vendorSha256 = null;
}
```

This file uses the `buildGoModule` Nix function to execute the build. Since `vendorSha256` is set to null, we must run `go mod vendor` in the project's root directory before running our build. Once we've done this, we can run `nix build` and see our binary in the `result/bin` directory.

If you are working on a NixOS install that does not currently have an active Go installation, you can activate one by running `nix-shell -p go`.
