---
title: Wireguard-Docker
filename: Wireguard-Docker.md
--- 
### Installing Docker On the Droplet
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

This is a series of commands to uninstall and outdated packages, reinstall docker and add the key to our systen, then run a sample Hello-World docker container to validate our installation 

Followed the installation guide from [PiHole Docker Project](https://nsr3953.github.io/DockerProject)

### Installing WireGuard

Following the [Installation Guide](https://thematrix.dev/setup-wireguard-vpn-server-with-docker/)

```bash
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
```
This creates a directory for our wireguard container 


```yml
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=174.138.48.78
      - SERVERPORT=51820
      - PEERS=pc1,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```
This is out yaml file for our docker. We edit it to set our timezone correctly, set our SERVERURL to the IP of our droplet, and change PEERS to create a config file for the computer and the phone


```bash
cd ~/wireguard/
docker compose up -d
docker compose logs -f wireguard
```
Starting our docker container and pulling up the QR codes to connect 


### Connecting To Wireguard

#### Computer

![[Pasted image 20231204152348.png]]

![[Pasted image 20231204152416.png]]

### Phone 

![image](https://github.com/nsr3953/nsr3953.github.io/assets/100631946/dbbdc57e-10f8-4587-a3e6-54f0dba6c363)

![image](https://github.com/nsr3953/nsr3953.github.io/assets/100631946/30182dcb-593f-4c95-b8f7-b1aa25ce2c25)


