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

Where `<gateway_address>` is whatever the `route get default` command returned.

If you find that you can't ping the gateway check to see if you are still connected to the wifi.

```shell
ifconfig en0 | grep inet
```

If you get an address or not try toggling Wifi on and off

```shell
networksetup -setairportpower en0 off
sleep2
networksetup -setairportpower en0 on
```

## Ping one of the Google DNS

```shell
ping -c 4 8.8.8.8
```

This checks to see if you are getting to the internet.

