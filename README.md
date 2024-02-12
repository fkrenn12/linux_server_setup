# **Linux Debian Server setup**
Instruction and scripts for setup Linux Debian server 
## On newly created server - before starting the installation 
👉 Start putty<br>
👉 Enter IP-Address and port number 22<br>
👉 Login as **root** or sudo user
👉 Create and add server administration user and add to sudo<br>
Modify "admin" for your own choice 
```sh
PUBLIC_KEY="Copy public key from puttygen and insert here"
```
```sh
SSH_PORT=250
USERNAME='admin'
APPNAME='YourApplicationName'
```

```sh
sudo echo "### Creating $USERNAME ###"
sudo adduser $USERNAME
sudo usermod -aG sudo $USERNAME
# ssh access
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
        
sudo echo "### Creating $APPNAME ###"
sudo adduser $APPNAME
# sudo usermod -aG sudo $APPNAME
# ssh access
sudo mkdir -p /home/$APPNAME/.ssh && sudo touch /home/$APPNAME/.ssh/authorized_keys
sudo chmod 700 /home/$APPNAME/.ssh && sudo chmod 600 /home/$APPNAME/.ssh/authorized_keys
sudo chown -R $APPNAME /home/$APPNAME/.ssh
sudo echo $PUBLIC_KEY >> /home/$APPNAME/.ssh/authorized_keys
sudo echo "
Match User $APPNAME
        X11Forwarding no
        AllowTcpForwarding yes
        GatewayPorts yes
        PermitTunnel yes" > /etc/ssh/sshd_config.d/$APPNAME.conf
```

```sh
sudo echo "Port $SSH_PORT" >> /etc/ssh/sshd_config
sudo echo 'PubkeyAuthentication yes' >> /etc/ssh/sshd_config
sudo sed -i '/^PermitRootLogin[ \t]\+\w\+$/{ s//PermitRootLogin no/g; }' /etc/ssh/sshd_config
sudo sed -i '/^PasswordAuthentication[ \t]\+\w\+$/{ s//PasswordAuthentication no/g; }' /etc/ssh/sshd_config
```

```sh
sudo systemctl restart ssh.service
sudo reboot
```
Login as admin user over ssh with publickey authentication on new port 

## Installation
👉 Copy and run installation scripts
```sh
sudo apt-get -y update
sudo apt-get -y install mc
sudo apt-get -y install htop
sudo apt-get -y install git
sudo git --version
```

👉 Docker installation<br>
Look here for details 
[Docker installation on Debian](https://docs.docker.com/engine/install/debian/)<br>
- Set up Docker's apt repository.
- Add the repository to Apt sources 
- Installing packages
```sh
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
sudo echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Postinstallation - allow manage Docker as a non-root user
```sh
APPNAME='YourApplicationName'
sudo groupadd docker
sudo usermod -aG docker $APPNAME
```

👉 Docker CLI 👉 Docker compose<br>
Maybe already done by install?
