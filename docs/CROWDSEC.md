# Installazione e configurazione Crowdsec

**CrowdSec** è un software di sicurezza open source che monitora i log (es. SSH e web server), riconosce pattern di attacco (come brute force e scansioni) e poi applica contromisure, tipicamente bloccando gli IP malevoli tramite componenti di “enforcement” (bouncer) o integrazioni con firewall/proxy


## Aggiungere il repository ufficiale

```dockerfile
curl -s https://packagecloud.io/install/repositories/crowdsec/crowdsec/script.deb.sh | sudo bash

#The repository is setup! You can now install packages.
```

## Installare CrowdSec + lettura log

```dockerfile
sudo apt install -y crowdsec crowdsec-firewall-bouncer-iptables

sudo reboot          #quando finisce

sudo systemctl enable crowdsec
sudo systemctl start crowdsec
sudo systemctl status crowdsec

```
Se lo stato è enabled, e active, procediamo.



```dockerfile


```
