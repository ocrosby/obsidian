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

### Reserve the Hostname & IP in Your Router (Static DHCP)

- This ensures your machine will always get the same IP address.
- The hostname will resolve consistently via DNS or mDNS


### Setup SSH Servers

On any MacOS machine that you want to SSH into execute the following:

```shell
sudo systemsetup -setremotelogin on
```

Note: Depending on the terminal you are on you may need to grand your terminal full disk access on Macos:

#### **✅ How to Grant Terminal Full Disk Access**

1. Open **System Settings** → **Privacy & Security**
    
2. Scroll to **Full Disk Access**
    
3. Click the **“+” button**
    
4. Select **Terminal** (or iTerm2 if you use that)
    
5. Toggle it to **enabled** (green)


You can verify:

```shell
sudo systemsetup -getremotelogin
```


Then generate an SSH key on the client that you want to connect from:

```shell
ssh-keygen -t ed25519 -C "<username>@<hostname>"
```

So for example if you are on a machine called `bubble` and your username is `fred`

```shell
ssh-keygen -t ed25519 -C "fred@bubble"
```

Copy your public key to the server you want to connect to

```shell
ssh-copy-id fred@myserver.local
```

This assumes you are attempting to ssh into a machine named `myserver.local`.

