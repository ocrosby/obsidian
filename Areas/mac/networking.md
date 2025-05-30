---
aliases: ["Networking Terminal Commands"]
---

## Restart Networking on MacBook

```shell
sudo ifconfig en0 down
sudo ifconfig en0 up
sudo ipconfig set en0 DHCP
```

## Get Your IP Address

```shell
ipconfig getifaddr en0
```

## Get The Gateway Address

```shell
route get default
```

## Ping the Gateway

```shell
ping -c 4 <gateway_address>
```

Where <gateway_address> is whatever the `route get default` command returned.

## Ping one of the Google DNS

```shell
ping -c 4 8.8.
```