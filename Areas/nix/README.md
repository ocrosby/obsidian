---
aliases: ["Introduction to Nix"]
---

## What is it?

Nix is a tool that takes a unique approach to package management and system configuration. Learn how to make reproducible, declarative and reliable systems.

## Installation

```shell
curl --proto '=https' --tlsv1.2 -L https://nixos.org/nix/install > nix-install.sh
chmod +x nix-install.sh
./nix-install.sh
# Follow the interactive prompts.
```

### Edit `/etc/nix/nix.conf`

```shell
sudo vim /etc/nix/nix.conf
```

Save this file:

```plaintext
build-users-group = nixbld
experimental-features = nix-command flakes
```

Nix flakes are a big paradigm shift in the nix community.

## Nix Manual

Nix is a package manager which comes in a form of many command line tools. Packages that Nix can build are defined with the Nix Expression Language.

## Nixpkgs Manual

The Nix Packages collection (Nixpkgs) is a set of thousands of packages for the Nix package manager and NixOS Linux distribution.

## References

- [Nix Manual](https://nixos.org/manual/nix/stable)
- [Nixpkgs Manual](https://nixos.org/manual/nixpkgs/stable)
- [First Steps](https://nix.dev/tutorials/first-steps/)
- [How Nix works](https://nixos.org/guides/how-nix-works/)


## Post-Installation

Nix requires a restart of your terminal environment once installed, so shut everything down and start a new terminal session.

### Install Nix Packages

```shell
nix run 'nixpkgs#hello'
```

