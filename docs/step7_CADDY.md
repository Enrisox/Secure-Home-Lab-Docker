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

### Dynamic IP management with Cloudflare DDNS

The **Cloudflare DDNS container** keeps DNS records updated automatically so that my domain and subdomains always point to the Raspberry Pi, even when the ISP changes my public IP.

- Periodically queries your public IP (IPv4 and/or IPv6) via external services or DNS-over-HTTPS.
- Compares it with the A/AAAA records in Cloudflare.
- If the IP has changed, calls the Cloudflare API to update the DNS records for your domain/subdomains.


## Caddy as reverse proxy
- **Automatic HTTPS with Let’s Encrypt certificates**.
- **Reverse proxy routing to internal containers**.
- Security-focused defaults: minimal configuration exposure, **TLS enforced by default**.
- Separation of concerns: **Cloudflare manages edge security (DDoS, WAF, bot mitigation), Caddy manages internal traffic safely.**

**Caddy must be attached to the same Docker network as the services it routes to**: for example: Reverseproxy_net

I created a network with Portainer but the docker command is : docker network create reverseproxy_net.

## Docker compose for the Caddy container
```bash
version: '3.8'
services:
  caddy:
    image: caddy:latest
    container_name: caddy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /home/enrico/caddy/config/Caddyfile:/etc/caddy/Caddyfile
      - caddy_data:/data
    networks:
      - reverseproxy_net   # <─ same network of my quiz-app

volumes:
  caddy_data:

networks:
  reverseproxy_net:
    external: true
```

## Caddyfile in my QUIZ-app ubuntu server directory
```bash
quizapp.enrisox-devops.it {     #container name.my domain
  reverse_proxy quizapp:9000    #internal port
}
```

Once configured, Caddy will securely route traffic from Cloudflare to the internal containers, keeping the infrastructure safe while ensuring HTTPS access.

