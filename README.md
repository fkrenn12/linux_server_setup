# 🚀 Linux Server setup (Debian) 🚀
Instructions and scripts for setup Linux Debian server for running a docker application 
## On newly created server - before starting the installation 
👉 Start putty<br>
👉 Enter IP-Address and port number 22<br>
👉 Login as **root** or sudo user<br>

### Create users with publickey ssh access  

On Windows you can use "puttygen" to create publickeys. 
The best secure option is to generate two separate keys with passphrase.<br>
* Define temporary variables for keys on host.

* Define temporary variables for ssh port and names with publickey on host.
```sh
SSH_PORT=250
read -p 'Administrator-Username: ' USERNAME
echo 'Generate the keypair and copy publickey'
read -p "Paste public Key for $USERNAME :" PUBLIC_KEY_USER
read -p 'Appname: ' APPNAME
echo 'Generate the keypair and copy publickey'
read -p "Paste public Key for $APPNAME :" PUBLIC_KEY_APP
```

* Creating users with ssh access and secured ssh ( no root login, publickey enabled ) 

```sh
create_ssh_access(){
  sudo mkdir -p /home/$1/.ssh && sudo touch /home/$1/.ssh/authorized_keys
  sudo chmod 700 /home/$1/.ssh && sudo chmod 600 /home/$1/.ssh/authorized_keys
  sudo chown -R $1 /home/$1/.ssh
  echo $2 | sudo tee -a /home/$1/.ssh/authorized_keys > /dev/null
  sudo echo "Match User $1
        X11Forwarding no
        AllowTcpForwarding yes
        GatewayPorts yes
        PermitTunnel yes" > /etc/ssh/sshd_config.d/$1.conf
}
sudo echo "### Creating $USERNAME ###"
sudo adduser --gecos GECOS $USERNAME
sudo usermod -aG sudo $USERNAME
sudo bash -c "$(declare -f create_ssh_access); create_ssh_access $USERNAME '$PUBLIC_KEY_USER'"
sudo echo "### Creating $APPNAME ###"
sudo adduser --gecos GECOS $APPNAME
# sudo usermod -aG sudo $APPNAME
sudo bash -c "$(declare -f create_ssh_access); create_ssh_access $APPNAME '$PUBLIC_KEY_APP'"
# configure ssh
echo "Port $SSH_PORT" | sudo tee -a /etc/ssh/sshd_config > /dev/null
echo 'PubkeyAuthentication yes' | sudo tee -a /etc/ssh/sshd_config > /dev/null
sudo sed -i '/^PermitRootLogin[ \t]\+\w\+$/{ s//PermitRootLogin no/g; }' /etc/ssh/sshd_config
sudo sed -i '/^PasswordAuthentication[ \t]\+\w\+$/{ s//PasswordAuthentication no/g; }' /etc/ssh/sshd_config
```

* Restart ssh service and reboot
```sh
sudo systemctl restart ssh.service
sudo reboot
```
👉 Relogin as admin user over ssh with publickey authentication on new port now

## Installation
👉 Copy and run installation scripts
```sh
sudo apt-get -y update
sudo apt-get -y install mc
sudo apt-get -y install htop
sudo apt-get -y install git
sudo git --version
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
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

- Postinstallation - allows managing Docker as a ### non-root ### user
```sh
APPNAME='YourApplicationName'
sudo groupadd docker
sudo usermod -aG docker $APPNAME
```

