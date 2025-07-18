```shell
sudo apt update
sudo apt upgrade -y
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
sudo hostnamectl set-hostname kube-studio
sudo apt install -y neovim
sudo apt install -y ripgrep
sudo apt install -y stow
sudo systemctl enable avahi-daemon
sudo systemctl start avahi-daemon

# verify it's working
systemctl status avahi-daemon
```