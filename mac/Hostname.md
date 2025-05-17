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

### Updating /etc/hosts

If you want to associate your hostname with 127.0.0.1, edit `/etc/hosts`

Add a line like:

```plaintext
127.0.0.1 <hostname>.local <hostname>
```

### Reserve the Hostname & IP in Your Roter (Static DHCP)

- This ensures your machine will always get the same IP address.
- The hostname will resolve consistently via DNS or mDNS