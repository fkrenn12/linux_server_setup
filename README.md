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
apt-get update
apt-get install ca-certificates curl
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc
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

👉 Docker CLI<br>
👉 Docker compose<br>