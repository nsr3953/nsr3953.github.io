---
title: DockerProject
filename: DockerProject.md
--- 

# Installing Docker

```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
This ensures that no old/unsopported docker packages are installed 

```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

This is to add dockers key

```
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```
Adding dockers site to our source list, and install necessary packages, and validate installation.

https://docs.docker.com/engine/install/ubuntu/


# Installing PiHole

https://pimylifeup.com/pi-hole-docker/

```
docker pull pihole/pihole
mkdir pihole
cd pihole
```
Grabbing our pihole docker image

```
nano docker-compose.yml
```
```
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "8000:80/tcp"
    environment:
      TZ: 'America/Chicago'
      WEBPASSWORD: 'password'
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

Creating our docker compose file

```
systemctl stop systemd-resolved
systemctl disable systemd-resolved
```
Stopping our OS's resolve service since the pihole will do this for us

```
nano /etc/resolv.conf
```
Changing the nameserver to point to cloudflare's (1.1.1.1)

```
docker-compose up -d
```
Finally starting our docker container

![image](https://github.com/nsr3953/nsr3953.github.io/assets/100631946/c1d2bb54-c411-4bf2-b110-5d3609ecff9f)

