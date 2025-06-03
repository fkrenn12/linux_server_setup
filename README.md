# 🚀 Linux Server setup (Debian) 🚀
Instructions and scripts for setup Linux Debian server for running a docker application 
## On newly created server - before starting the installation 
👉 Start putty<br>
👉 Enter IP-Address and port number 22<br>
👉 Login as **root** or sudo user<br>

### Create users with publickey ssh access  

On Windows you can use "puttygen" to create publickeys. 
The best secure option is to generate two separate keys with passphrase.<br>

* Define temporary variables for ssh port and names with publickey on host.
```sh
SSH_PORT=22
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
  sudo chmod go-w /home/$1
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
sudo mkdir /home/$APPNAME/dummy-project
# adding admin to application-group
sudo usermod -aG $APPNAME $USERNAME
sudo bash -c "$(declare -f create_ssh_access); create_ssh_access $APPNAME '$PUBLIC_KEY_APP'"
# configure ssh
echo "Port $SSH_PORT" | sudo tee -a /etc/ssh/sshd_config > /dev/null
echo 'PubkeyAuthentication yes' | sudo tee -a /etc/ssh/sshd_config > /dev/null
sudo sed -i '/^PermitRootLogin[ \t]\+\w\+$/{ s//PermitRootLogin no/g; }' /etc/ssh/sshd_config
sudo sed -i '/^PasswordAuthentication[ \t]\+\w\+$/{ s//PasswordAuthentication no/g; }' /etc/ssh/sshd_config
sudo systemctl restart ssh.service
sudo reboot
```
👉 Relogin now as admin user over ssh with publickey authentication on the new port

## Installation
👉 Copy and run installation scripts<br>
This will install: mc, htop, git, gh, fail2ban<br>

```sh
sudo apt-get -y update
sudo apt-get -y install mc
sudo apt-get -y install wget
sudo apt-get -y install unzip
sudo apt-get install rsync
sudo apt-get -y install htop
sudo apt-get -y install git
sudo git --version
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt install gh -y
sudo apt install fail2ban -y
echo "backend = systemd" | sudo tee -a /etc/fail2ban/jail.d/defaults-debian.conf > /dev/null
sudo systemctl restart fail2ban.service
```
Check fail2ban running with:
```sh
sudo systemctl status fail2ban.service
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
sudo groupadd docker
sudo usermod -aG docker $APPNAME
```
Einloggen bei Github
```sh
gh auth login
```
Answer questions 
? What account do you want to log into? GitHub.com<br>
? What is your preferred protocol for Git operations? HTTPS <br>
? Authenticate Git with your GitHub credentials? No <br>
? How would you like to authenticate GitHub CLI? Login with a web browser<br>

There will be a failed login! <br>
Browse to https://github.com/login/device and authenticate with mobile auth.

Datenbank kopieren
sudo cp /var/lib/docker/volumes/{volumname}/_data /home/{username}/_data
chown -R <user>:<group> /home/{username}/_data
mobaxterm benutzen und in ziel volume kopieren