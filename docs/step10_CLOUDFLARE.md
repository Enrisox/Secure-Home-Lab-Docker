# Cloudflare DDNS and Caddy, Reverse Proxy with SSL

This step describes how to configure a custom domain, Cloudflare for DNS management, dynamic IP updates via a DDNS container, and a secure reverse proxy with Caddy, including automatic SSL certificates.
Cloudflare and Caddy both act as reverse proxies, but at different layers and responsibility domains:

- Cloudflare operates as an edge proxy (Internet-facing).
- Caddy operates as an origin proxy (LAN/host-level), routing traffic to containers and hiding the hosted containers behind it.

**Free plan protections included**

- **Bot fight mode with JS detections: On**
Cloudflare identifies automated traffic and can issue “computational” challenges to bots. The JS Detection runs on each request to help identify automation.
- **Browser integrity Check**
Evaluates HTTP headers and can block requests considered suspicious.
- **Challenge passage 30 min**
Once a client passes a challenge, it is “validated” for 30 minutes before receiving another one.
- **HTTP DDoS + network-layer DDoS protection (“Always active”)**
Automatic mitigations for HTTP and network-level DDoS attacks are always enabled.
- **Cloudflare managed ruleset (WAF) (“Always active”)**
A Cloudflare-managed WAF ruleset protects against common exploits; users can review and enable rules based on their stack.
- **AI labyrinth**
Inserts invisible “nofollow” links to trap non-cooperative crawlers, consuming bot resources and helping identify aggressive scrapers.

## Cloudflare configuration

1. At your domain registrar, go to DNS settings.
2. Disable DNSSEC if enabled.
3. Update the nameservers to the ones provided by Cloudflare and save. Cloudflare will now manage all DNS queries for your domain; you no longer need to access the registrar portal.
4. In Cloudflare, **create A (IPv4) records for your domain and subdomains**, pointing to your public IP. Set the Proxy status to Proxied (orange cloud).
5. Create an **API Token** in **Cloudflare** with **Zone:Edit permissions for your domain**. This will be required by the DDNS container.

## Dynamic IP management with Cloudflare DDNS

The **Cloudflare DDNS container** keeps DNS records updated automatically so that my domain and subdomains always point to the Raspberry Pi, even when the ISP changes my public IP.

- Periodically queries your public IP (IPv4 and/or IPv6) via external services or DNS-over-HTTPS.
- Compares it with the A/AAAA records in Cloudflare.
- If the IP has changed, calls the Cloudflare API to update the DNS records for your domain/subdomains.

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

- CF_API_TOKEN=*********
- DOMAINS=domain.it, subdomain.it
- IP_VERSION=4, IP6_PROVIDER=none, PROXIED=false

The cloudflare-ddns container in my environment does one thing, but it's essential: it keeps my domain IP addresses updated on Cloudflare, so they always point to my current public IP.


1. Every 5 minutes it detects my public IPv4 IP.
2. Checks the DNS records on Cloudflare for the domains I ve set in DOMAINS .
3. If the IP in the A records is different from the current one, it updates them automatically; if it's the same, it doesn't touch anything.
