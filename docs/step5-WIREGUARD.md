# WIREGUARD stack configuration

1) Create volume wireguard_config.
2) Create a new stack called wireguard and paste this YAML file.

```bash
version: "3.8"
services:
  wireguard:
    image: linuxserver/wireguard:latest
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    network_mode: host        # network type is host
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Rome
      - SERVERURL= auto or set public IP    # set public IP!
      - SERVERPORT=******    ## set any port you like, e.g.: 51076
      - PEERS=2           
      - PEERDNS=*******       #my private ubuntu server IP
      - INTERNAL_SUBNET=       #private network different from the LAN private network, e.g.: 10.10.10.0
      - LOG_CONFS=true         #LOG_CONFS=true will make QR codes appear in the logs.

    volumes:
      - wireguard_config:/config    #contains the peers’ private keys
      -/lib/modules:/lib/modules
    restart: unless-stopped

    ports:
      - 50000:50000/udp        #the port I chose
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped

volumes:
  wireguard_config:
    external: true

```

To finish, scan the QR code with the Wireguard app on the mobile device and check that the data is correct (IP and ports).

On the router you must configure port forwarding for the chosen port, on your home router!

## Dynamic IP management with Cloudflare DDNS

The **Cloudflare DDNS container** keeps DNS records updated automatically so that my domain and subdomains always point to the Raspberry Pi, even when the ISP changes my public IP.

- Periodically queries my public IP (IPv4 and/or IPv6) via external services or DNS-over-HTTPS.
- Compares it with the A/AAAA records in Cloudflare.
- If the IP has changed, calls the Cloudflare API to update the DNS records for my domain/subdomains.

```bash
version: "3"

services:
  cloudflare-ddns:
    image: favonia/cloudflare-ddns:latest
    container_name: cloudflare-ddns
    restart: unless-stopped
    environment:
      - CF_API_TOKEN= *********                 #Put your CLOUDFLARE API-TOKEN here
      - CF_ZONE=enrisox-devops.it        # Cloudflare zone
      - DOMAINS=*********, **********    # my domain and subdomains
      - CF_TTL=auto
      - IP_VERSION=4
      - IP6_PROVIDER=none
```

Instead of the oznu/cloudflare-ddns image (which was giving 400 errors for credentials), I switched to the favonia/cloudflare-ddns container, configured with:

- CF_API_TOKEN=...
- DOMAINS=enrisox-devops.it,quizapp.enrisox-devops.it
- IP_VERSION=4, IP6_PROVIDER=none, PROXIED=false

The cloudflare-ddns container in my environment does one thing, but it's essential: it keeps my domain IP addresses updated on Cloudflare, so they always point to my current public IP.


1. Every 5 minutes it detects my public IPv4 IP.
2. Checks the DNS records on Cloudflare for the domains I ve set in DOMAINS .
3. If the IP in the A records is different from the current one, it updates them automatically; if it's the same, it doesn't touch anything.
