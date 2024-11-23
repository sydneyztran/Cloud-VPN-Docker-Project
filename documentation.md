# Cloud VPN-Docker Project - Installation Process
*Sydney Tran 1585868 - CYB3353 Project 3*

## Creating a DigitalOcean.com (DO) Account
I signed up for a new account on DO using the URL: https://m.do.co/c/d33d59113ab6. With this link,I was able to earn free credit for 60 days. 

## Creating a DO Ubuntu Droplet
I then navigated to the Droplet page on DO and created a Droplet with the following characteristics:
- Ubuntu 24.04
- Basic
- Regular Intel CPU 
- Normal SSD

Additionally, I chose to create a root password instead of an SSH key and chose the San Francisco data center. 

## Installing Docker on Droplet
To install Docker, I used the following link as a guide/reference: https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/.

I then opened the console on my created DO Droplet and used the following commands:
- Installed necesssary tools: `sudo apt install apt-transport-https ca-certificates curl software-properties-common -y`
- Added a docker key: `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
- Added a 32bit/64bit OS: `sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"`
- Switched to the correct repo: `apt-cache policy docker-ce`
- Installed Docker: `sudo apt install docker-ce -y`
- Installed Docker-Compose: `sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
- Set permissions: `sudo chmod +x /usr/local/bin/docker-compose`

With these commands, I was able to successfully install Docker and Docker-Compose on Ubuntu. 

## Installing Wireguard Using Docker-Compose
Next, I used this link, https://thematrix.dev/setup-wireguard-vpn-server-with-docker/, to setup Wireguard VPN server with Docker. 
I inputted the following commands on my console:
- Created necessary files with: `mkdir -p ~/wireguard/`, `mkdir -p ~/wireguard/config/`, and `nano ~/wireguard/docker-compose.yml`. This allowed me to create and open a docker-compose file. 
- In the docker-compose file, I inputted the following content:
```yamlversion: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=137.184.233.0
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
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
- After saving and exiting the file, I used the command `cd ~/wireguard/` and `docker-compose up -d` to start Wireguard.

## Testing VPN Connection
After Wireguard was created, I connected by phone to Wireguard by downloading the Wireguard app on my phone. 

I then ran the command, `docker-compose logs -f wireguard`, to output a QR code that I scanned with the app. 

Before enabling the VPN, I made sure to visit IPLeak.net to make screenshots of the page showing my current IP address (and other details). 

After turning on the VPN tunnel, I then revisit the IPLeak.net page where I found that my IP address was different than it was before. 

After making necessary screenshots, I was finished with the project. 