# **Linux Debian Server setup**
Instruction and scripts for setup Linux Debian server 
## On newly created server - before starting the installation 
👉 Start putty<br>
👉 Enter IP-Address and port number 22<br>
👉 Login as **root** or sudo user
👉 Create and add server administration user and add to sudo<br>
Modify "admin" for your own choice 
```sh
PUBLIC_KEY="AAAAASSS"
USERNAME='admin1'
```

```sh
sudo adduser $USERNAME
sudo usermod -aG sudo $USERNAME
sudo mkdir -p /home/$USERNAME/.ssh && sudo touch /home/$USERNAME/.ssh/authorized_keys
sudo chmod 700 /home/$USERNAME/.ssh && sudo chmod 600 /home/$USERNAME/.ssh/authorized_keys
sudo chown -R $USERNAME /home/$USERNAME/.ssh
sudo echo $PUBLIC_KEY >> /home/$USERNAME/.ssh/authorized_keys
sudo echo "
Match User $USERNAME
        X11Forwarding no
        AllowTcpForwarding yes
        GatewayPorts yes
        PermitTunnel yes" > /etc/ssh/sshd_config.d/$USERNAME.conf

```

```sh
systemctl restart ssh.service
```

## Installation
👉 Copy and run installation scripts
```sh
apt-get -y update
apt-get -y install mc
apt-get -y install htop
apt-get -y install git
git --version
```

👉 Create and add application user/group<br>
Modify application_name
```sh
sudo  useradd -d /home/application_name -p password application_name
```
👉 Docker installation<br>
Look here for details 
[Docker installation on Debian](https://docs.docker.com/engine/install/debian/)<br>
- Set up Docker's apt repository.
```sh
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
- Add the repository to Apt sources 

```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
```
- Next step - installing packages
```sh
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin```
```
- Postinstallation Step - Manage Docker as a non-root user
```sh
groupadd docker
sudo usermod -aG docker application_name
```

👉 Docker CLI 👉 Docker compose<br>
Maybe already done by install?
