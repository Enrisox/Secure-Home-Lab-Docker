# UFW firewall configuration

**Command to show UFW Linux firewall status:**
```bash
sudo ufw status verbose
```

**I added rules to block all incoming traffic and allow outgoing traffic freely:**
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

**I only allowed the services I need: Caddy and WireGuard**
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 53000/udpsudo ufw allow 53000/udp     #assuming a port 53000 set in the router's port-forwarding
sudo ufw allow from 192.168.4.0/24 to any port 22 proto tcp       #SSH access from devices on the LAN
```
## SSH rule only via VPN
When you are connected to the VPN, your client receives an internal IP (e.g. 10.8.0.x).
To ensure that only those on the VPN can SSH:

```bash
sudo ufw allow from 10.8.0.0/24 to any port 22 proto tcp
```

Explanation:
10.8.0.0/24 → subnet WireGuard I choose
to any port 22 →  SSH access
proto tcp →  TCP protocol

**Enable the firewall:**
```bash
sudo ufw enable
```

```bash
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
22/tcp                     ALLOW IN    rete_privata-del-server/S.M
22/tcp                     ALLOW IN    rete-privata-virtuale(VPN)/S.M
80/tcp (v6)                ALLOW IN    Anywhere (v6)
443/tcp (v6)               ALLOW IN    Anywhere (v6)
53000/udp (v6)             ALLOW IN    Anywhere (v6)       #VPN port
```
UFW keeps a log of blocked or allowed packets.
"Low" level means it only logs suspicious or blocked packets, not every single packet.
Allows monitoring of intrusion attempts or suspicious access without saturating the disk.

## Summary
The firewall is configured with a default deny policy for incoming and routed traffic, allowing only the traffic necessary for services (SSH LAN/VPN, Web, WireGuard), while outgoing traffic is unrestricted. This approach drastically reduces the attack surface and ensures monitoring through logs.

Better to focus on potential attacks from outside, and filter only packets coming from IPs outside my LAN:

```bash
sudo grep "BLOCK" /var/log/ufw.log | grep -v "192.168.4."           #se server è sulla 192.168.4.0/24 ad esempio
```
