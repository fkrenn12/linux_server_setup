# **Linux server setup**
Instruction and scripts for setup linux server 
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
👉 Docker CLI<br>
👉 Docker compose<br>