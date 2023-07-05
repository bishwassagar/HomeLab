# HomeLab

## Hardware Config (Digital Ocean Droplet)

|                 |                                |
| --------------- | ------------------------------ |
| OS:             | Ubuntu 22.10 kinetic           |
| Kernel:         | x86_64 Linux 5.19.0-46-generic |
| Disk (SSD)      | 80GB                           |
| CPU             | 2 Intel vCPUs                  |
| Memory          | 4GB                            |
| Data Centre     | BLR1                           |
| Hashicorp Vault | Partial                        |

# This repo contains guide and config which worked out for me but might not be the same case for you. ⚠️⚠️

## Wireguard

Image: [linuxserver/wireguard](https://github.com/linuxserver/docker-wireguard)

### Installation Guide

### docker-compose (recommended)

This is my configured yaml which worked for me and my location. You can just download this config file: \
`wget github.com/bishwassagar/HomeLab/wireguard/docker-compose.yml` \
`docker-compose up -d` \
and you're good to go.

```yaml
---
version: "2.1"
services:
  wireguard:
    image: lscr.io/linuxserver/wireguard:latest
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Kolkata
      - SERVERURL=auto
      - SERVERPORT=51820
      - PEERS=5
      - PEERDNS=1.1.1.1,1.0.0.1
      - INTERNAL_SUBNET=10.13.13.0
      - ALLOWEDIPS=0.0.0.0/0
      - PERSISTENTKEEPALIVE_PEERS=
      - LOG_CONFS=true
    volumes:
      - /opt/wireguard/config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: always
```

Specify a timezone if you're from another region, see this [list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List).

    - TZ=Asia/Kolkata

If you've a domain then specify that here or just let it be on auto.

    - SERVERURL=auto

I'm using Cloudflare DNS but you can use the one you like.

    - PEERDNS=1.1.1.1,1.0.0.1

### Errors encountered and fixes

I encountred these errors and this is how I fixed it.

    - Bind for 0.0.0.0:<port-no> failed: port is already allocated

```shell
lsof -i :<port-no>
kill -9 <process-id>
```

### Usage

The files have been configured in `/opt/wireguard/config/peer*` folder. Navigate to any of those folders and you'll get the peer*.png and peer*.conf files.

You can either use `scp` or just run `python3 -m http.server 80` to get the files out of server.

That's it. Scan or use conf file to connect to VPN.
