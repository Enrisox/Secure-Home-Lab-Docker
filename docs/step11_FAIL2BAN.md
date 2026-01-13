# FAIL2BAN

Do CrowdSec and Fail2Ban still make sense with SSH key authentication?
Yes, it still makes sense to keep them, even if using a public SSH key makes unauthorized access nearly impossible.

## Why keep them?

### Reducing server load
Even if bots cannot log in without the private key, they will still attempt SSH connections, generating logs and consuming server resources (CPU, memory, connections). Fail2Ban and CrowdSec block these IPs after a few attempts, reducing log noise and server load.

### Protection for other services
An IP scanning your SSH port is likely probing other services too (HTTP ports, unpatched services, etc.). Blocking it at the firewall after SSH attempts also protects Caddy, Portainer, AdGuard, and other exposed containers.

### Community intelligence with CrowdSec
CrowdSec shares malicious IP information among users, so you get proactive protection from known attackers even before they target your SSH.

---

## Recommended configuration

**Fail2Ban** in aggressive mode for SSH with public key authentication (counts attempts with wrong keys, not only passwords):

Keep CrowdSec to protect Caddy/HTTP services and benefit from the shared blocklist.


# Further SSH Server Hardening
Fail2Ban and CrowdSec can coexist without conflicts:  
- **Fail2Ban**: reactive and fast on SSH (immediate local ban).  
- **CrowdSec**: more complex analysis, community blocklist, shared decision-making.

## Fail2Ban Installation

```bash
sudo apt update
sudo apt install fail2ban -y
```

**Fail2Banuses configuration files in /etc/fail2ban/. Never modify the .conf files directly; always create overrides**
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

**Find the [DEFAULT] section (around line 90-100) and modify:**

```bash
[DEFAULT]
bantime = 3600        #Ban for 1 hour (3600 seconds)
findtime = 600       # Time window of 10 minutes to count attempts
maxretry = 3         # Maximum number of retries before ban
backend = systemd      # Backend to read logs (systemd for modern Ubuntu/Debian)
```

**Find the [sshd] section (around line 280-290) and verify/modify:**
```bash
[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
backend = systemd
maxretry = 3
bantime = 3600
findtime = 600
```
**Enable and start Fail2Ban:**
```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo systemctl status fail2ban
```
### Useful commands
```bash
sudo fail2ban-client status                 # Check active SSH jail
sudo fail2ban-client status sshd           # See currently banned IPs
sudo fail2ban-client set sshd unbanip <IP>  # Manually unban an IP (if accidentally blocked)
sudo fail2ban-client set sshd banip <IP>    # Manually ban an IP
sudo tail -f /var/log/fail2ban.log          # Fail2Ban logs
```
