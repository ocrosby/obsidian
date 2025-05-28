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


