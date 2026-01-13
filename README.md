# Secure-Home-Lab-Docker

**Welcome to my personal project dedicated to building a self-hosted, modular, and scalable environment based on:**

* Ubuntu Server
* Docker 
* Portainer
* AdGuard Home (DNS filtering)
* WireGuard (VPN)
* Caddy (reverse proxy)
* Ip tables/UFW 
* Cloudflare DDNS
* Crowdsec & FAIL2BAN
* Netdata

The purpose of this repository is to document step-by-step the configuration of the entire system in a simple, repeatable way, suitable for both VirtualBox environments and dedicated hardware like Raspberry Pi or mini PCs.
I tried to include the issues encountered and their solutions.  
**The project started in November 2025 and is still under constant integration and updates.**

📚**Table of Contents**

📄 [Step 1 – Ubuntu Server](docs/step1-UBUNTU-SERVER.md)  
📄 [Step 2 – Docker](docs/step2-DOCKER.md)  
📄 [Step 3 – Portainer](docs/step3-PORTAINER.md)  
📄 [Step 4 – AdGuard Home](docs/step4-ADGUARD.md)  
📄 [Step 5 – WireGuard](docs/step5-WIREGUARD.md)<br>
📄 [Step 6 – Raspberry Pi 5](docs/step6-RaspberryPi5.md)<br>
📄 [Step 7 – Caddy](docs/step7_CADDY.md)<br>
📄 [Step 8 – Ufw](docs/step8_FIREWALL.md)<br>
📄 [Step 9 – Crowdsec](docs/step9_CROWDSEC.md)<br>
📄 [Step 10 – Cloudflare](docs/step10_CLOUDFLARE.md)<br>
📄 [Step 11 – Fail2ban](docs/step11_FAIL2BAN.md)<br>
📄 [Step 12 – hardening](docs/step12_HARDENING_CONTAINERS.md)<br>


**🧱 Project Goals**

- **Centralized Home Server Infrastructure:** Implement a Docker-based home server environment to centralize and manage network services and self-hosted applications in a modular and efficient way.
- **Filter DNS traffic at the network level to block ads and tracking.**
- **Ensure secure remote access via VPN.**
- **Apply container hardening practices** (e.g., non-root users, read-only filesystem, capability limitations) to reduce the attack surface.
- **Deploy Custom Applications:** Host internally developed applications with a focus on modern architectures (reverse proxy with automatic HTTPS, isolated networks).
- **Reproducibility and Documentation:** Document the entire configuration (Infrastructure as Code) through docker-compose.yml files, scripts, and step-by-step guides to enable rapid disaster recovery and system replicability on other nodes.

⭐ **If you liked my project, give it a star!**


**Enrico Soci**

enricosoci@protonmail.com
