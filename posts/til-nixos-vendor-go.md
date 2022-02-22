---
title: "TIL: Determine vendorSha256 for Nix Go build"
date: 2022-02-22
tags:
  - til
  - nixos
  - linux
  - golang

layout: layouts/post.njk
---

To build a Go project using Nix, you can write a `default.nix` like this:

```nix
with import <nixpkgs> {};

buildGoModule {
        pname = "stomper";
        version = "0.0.1";

        src = ./.;

        vendorSha256 = null;
}
```

However that requires checking in your `vendor` folder to source control (or otherwise running `go mod vendor`) prior to `nix build`.

To write a nix build script that is able to run without a prior invocation of `go mod vendor`, we need to find the required value for `vendorSha256`. This is the hash of the output of an intermediate build step--where `buildGoModule` has vendored these dependencies for you.

We can find this hash by running `nix build` on the following script:

```nix
with import <nixpkgs> {};

buildGoModule {
        pname = "stomper";
        version = "0.0.1";

        src = ./.;

        # requires running go mod vendor prior to build
        # on NixOS, do this:
        # $ nix-shell -p go
        # $ go mod vendor
        # $ nix build

        vendorSha256 = lib.fakeSha256;
}
```

When we run this script, we get shell output like:

```shell-session
$ nix build
hash mismatch in fixed-output derivation '/nix/store/hznlxkjmfkqwh2fmpa840nqb13skwhm0-stomper-0.0.1-go-modules':
  wanted: sha256:0000000000000000000000000000000000000000000000000000
  got:    sha256:1dyc8wjpqpz4s3fracf89zw2zbvj355hh3phlbg99dhwaf4szhmf
cannot build derivation '/nix/store/7dwf06m2ln4dmq8bgzivp90cy37hic66-stomper-0.0.1.drv': 1 dependencies couldn't be built
[0 built (1 failed), 0.0 MiB DL]
error: build of '/nix/store/7dwf06m2ln4dmq8bgzivp90cy37hic66-stomper-0.0.1.drv' failed
```

We can simply substitute the hash listed after 'got' into our build script and it will now work correctly:

```nix
buildGoModule {
        pname = "stomper";
        version = "0.0.1";

        src = ./.;

        # requires running go mod vendor prior to build
        # on NixOS, do this:
        # $ nix-shell -p go
        # $ go mod vendor
        # $ nix build

        vendorSha256 = '1dyc8wjpqpz4s3fracf89zw2zbvj355hh3phlbg99dhwaf4szhmf';
}
```

I'm very much new to Nix (and not sure that I'll stick with it beyond experiments, even though it is cool), but this process helped me understand the shift in understanding the software build process that Nix attempts. Instead of seeing a software build as a "version", every software build is seen as exactly the result of a specific and (ideally) completely fixed and known set of build inputs. This includes everything from the build tools (here set up by the buildGoModule function) to the immediate source of your project to the source of the dependencies it requires.

Using the `vendorSha256` value to verify the intermediate derivation is similar to the `go` tool's use of `go.sum`.
