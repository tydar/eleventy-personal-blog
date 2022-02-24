---
title: "TIL: Build a Go project using gomod2nix"
date: 2022-02-23
tags:
  - til
  - nixos
  - linux
  - golang

layout: layouts/post.njk
---

* [gomod2nix](https://github.com/tweag/gomod2nix)
* People dislike `buildGoModule` because it introduces network access to the Nix build runner and for other issues: [some discussion here](https://github.com/NixOS/nix/issues/2270)
* gomod2nix is a code generation tool that gets the dependencies and creates a lockfile called `godmod2nix.toml`
* Then a Nix build script can use a nix function `buildGoApplication` to build based on that lockfile.

## Using gomod2nix as a flake

1) Enable the experimental 'flakes' feature. You can find instructions (and more information) [in this post](https://christine.website/blog/nix-flakes-1-2022-02-21) by Xe Iaso.
2) Write a `flake.nix` file in the root directory of your Go project. Here's an example I wrote for my project [stomper](https://github.com/tydar/stomper). I borrowed nearly verbatim from [aioproxy](https://github.com/contrun/aioproxy/blob/master/flake.nix) and also looked at [google/note-maps](https://github.com/google/note-maps/blob/main/flake.nix) for more context.

```nix
{
    inputs = {
        nixpkgs.url = "github:nixos/nixpkgs/nixpkgs-unstable";
        flake-utils.url = "github:numtide/flake-utils";
        gomod2nix.url = "github:tweag/gomod2nix";
        gomod2nix.inputs.nixpkgs.follows = "nixpkgs";
        gomod2nix.inputs.utils.follows = "flake-utils";
    };

    outputs = { self, nixpkgs, flake-utils, gomod2nix }:
        let
            out = system:
                let
                    pkgs = import nixpkgs {
                        inherit system;
                        overlays = [ gomod2nix.overlay ];
                    };
                in {
                        devShell = pkgs.mkShell { buildInputs = with pkgs; [ go ]; };

                        defaultPackage = pkgs.buildGoApplication {
                            pname = "stomper";
                            version = "latest";
                            goPackagePath = "github.com/tydar/stomper";
                            src = ./.;
                            modules = ./gomod2nix.toml;
                        };

                        defaultApp =
                            flake-utils.lib.mkApp { drv = self.defaultPackage."${system}"; };
                };
            in with flake-utils.lib; eachSystem defaultSystems out;
}
```

3) At the root directory of your project, run `$ nix run github:tweag/gomod2nix`. This should generate your `gomod2nix.toml` file.
4) Now, run `nix build`.

### Steps for using this on a non-flake project:
1) Clone and build gomod2nix (requires flakes)
2) from your project root, run gomod2nix.
3) copy the builder folder from gomod2nix to your project's root
4) Run nix-build in the root of your project.

```nix
let
   pkgs = import <nixpkgs> {
      overlays = [
         (self: super: {
            buildGoApplication = super.callPackage ./builder { };
         })
      ];
   };
in pkgs.buildGoApplication {
   pname = "stomper";
   version = "0.0.1";
   src = ./.;
   modules = ./gomod2nix.toml;
}
```

When I initially ran gomod2nix, it failed to fetch a package. Running it again resolve the issue. Additionally I was kicked out of my SSH session on the 2nd, seemingly successful run. I am not sure what caused that, but gomod2nix may be intended to produce the `builder` directory itself -- I'm not sure!
