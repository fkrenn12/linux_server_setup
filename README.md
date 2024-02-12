# **Linux Debian Server setup**
Instruction and scripts for setup Linux Debian server 
## Installation 
👉 Login as **root** on netcup servercontrol panel<br>
👉 Copy and run installation scripts
```sh
apt-get -y update
apt-get -y install mc
apt-get -y install git
git --version
```
👉 Create and add server administration user and add to sudo<br>
Modify "#admin#" for your own choice 
```sh
useradd -d /home/admin -p password admin
usermod -aG sudo admin
```
👉 Create and add application user/group<br>
Modify application_name
```sh
useradd -d /home/application_name -p password application_name
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

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
- Next step - installing packages
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin```
```sh

👉 Docker CLI<br>
👉 Docker compose<br>