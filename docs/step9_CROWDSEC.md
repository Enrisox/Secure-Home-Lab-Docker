# CrowdSec installation and configuration

**CrowdSec**is an open-source security software that monitors logs (e.g. SSH and web servers), recognizes attack patterns (such as brute force and scans) and then applies countermeasures, typically blocking malicious IPs through "enforcement" components (bouncers) or integrations with firewalls/proxies.

CrowdSec protects my server in two ways: it proactively blocks known malicious IPs (community blocklist) and reacts to suspicious behaviors it sees in my logs, generating bans (temporary or permanent).

## Add the official repository

```dockerfile
curl -s https://packagecloud.io/install/repositories/crowdsec/crowdsec/script.deb.sh | sudo bash

#The repository is setup! You can now install packages.
```

## Install CrowdSec + log reading

```dockerfile
sudo apt install -y crowdsec crowdsec-firewall-bouncer-iptables

sudo reboot          #when it finishes
sudo systemctl enable crowdsec
sudo systemctl start crowdsec
sudo systemctl status crowdsec

```
If the status is enabled and active, we proceed.


## Verify that the engine works

Show live statistics
E.g.: analyzed packets, active bans, alerts

```dockerfile
sudo cscli metrics
```

## Show which parsers are available

Parsers (in CrowdSec) are "rules" described in YAML files that explain how to interpret a log string (a log line, or a field extracted from a previous parser) and transform it into structured fields that scenarios can then work on.

```dockerfile
sudo cscli parsers list
```


## Show scenarios list

```dockerfile
sudo cscli scenarios list
```

"Scenarios" in CrowdSec are behavioral detection rules: they define a heuristic (in YAML) that correlates already parsed events (e.g. many failed login attempts from the same IP in a short time) and decides when that activity is suspicious/an attack.

Scenarios compare normalized logs from parsers with thresholds/time windows (count, timeframe, etc.) to recognize patterns such as brute force, scans, aggressive crawls.

When a scenario "triggers", CrowdSec generates an alert (for traceability) and can generate one or more decisions (e.g. temporary IP ban), which will then be applied by a bouncer like iptables.

## log live
```dockerfile
sudo journalctl -u crowdsec -f
```

## Bouncer Crowdsec
I didn't need to manually set the key because the iptables bouncer, installed via apt, can auto-register on the Local API when CrowdSec is on the same machine and generate the API key by itself (usually writing it to a .yaml.local file).
```dockerfile
sudo cat /etc/crowdsec/bouncers/crowdsec-firewall-bouncer.yaml.local
```

The CrowdSec bouncer is the component that implements the decisions made by the CrowdSec engine (ban, captcha, etc.): the engine detects the attack from the logs and saves a "decision" in the Local API, while the bouncer queries the Local API and applies the countermeasure at the right point (firewall, Cloudflare, reverse proxy, etc.).

In my case, the crowdsec-firewall-bouncer is a "firewall" bouncer: it applies bans at the iptables/nftables level on the server, blocking incoming traffic from "bad" IPs.

**Difference:**

- **CrowdSec** (engine/agent): analyzes logs → generates alerts/decisions.
- **Bouncer**: reads decisions from the Local API (authenticating with an API key) → applies them (blocks).

## View banned IPs (CrowdSec)
To see the IPs that CrowdSec has decided to ban (regardless of iptables), use:
The decision list is the command that shows active "decisions" saved in the CrowdSec Local API: basically the list of actions to apply and on which target (IP, range, etc.).

```dockerfile
sudo cscli decisions list

sudo ipset list crowdsec-blacklists-0 | head -n 30       #To list the IPv4 currently in the set (those that iptables is dropping)

sudo ipset list crowdsec6-blacklists-0 | head -n 20      #To list the IPv6 currently in the set (those that iptables is dropping)

```

