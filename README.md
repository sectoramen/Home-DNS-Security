# Home DNS Security

This repo implements two services using Docker:

1. PiHole to block adds across all your home devices.

2. CloudFlared to encrypt DNS requests leaving your home network.

The following assumptions are made ... because this is how I run it at home :-)

- The Server running PiHole and CloudFlared has an IP address of 192.168.1.4 (Either static or through DHCP reservation)
- PiHole will run DNS and not DHCP.
- Your router is at 192.168.1.1 and runs as a DHCP server
- You have installed docker and docker-compose on your system

You should modify the docker-compose.yaml and replace the following if your network has different IP addresses:

- Replace 192.168.1.1 with your router's LAN IP address
- Replace 192.168.1.4 with your server's IP address
- Uncomment the line with `- "67:67/udp"` if you want PiHole to serve as a DHCP server.

## Pre-Requisites

If your Linux distribution runs a DNS resolver you must disable the DNSSTubListener or PiHole will no be able to bind to port 53

`sudo sed -r -i.orig 's/#?DNSStubListener=yes/DNSStubListener=no/g' /etc/systemd/resolved.conf`
`sudo sh -c 'rm /etc/resolv.conf && ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf'`

Edit the /etc/resolv.conf and point your DNS to your router IP by ensuring this line is the top or only line in the file:

`nameserver 192.168.1.1`

Then restart the DNS resolver:

`systemctl restart systemd-resolved`

Create the docker network for the two containers by running:

`docker network create my_net --subnet 172.20.1.0/24`

Create the directories where the PiHole and CloudFlared configurations will reside

`mkdir PiHole CloudFlared`

Run the two containers:

`docker-compose up -d`
