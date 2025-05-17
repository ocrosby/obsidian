# Working with Hostnames Locally

### Set A local hostname

```shell
sudo scutil --set ComputerName "<hostname>"
sudo scutil --set HostName "<hostname>"
sudo scutil --set LocalHostName "<hostname>"
```

### Verifying local hostname

```shell
sudo scutil --get ComputerName
sudo scutil --get HostName
sudo scutil --get LocalHostName
```