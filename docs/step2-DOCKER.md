# Docker Installation

**To begin, it is necessary to:** <br>
**- Install the prerequisites**

```bash
sudo apt install ca-certificates curl gnupg -y

```

• ca-certificates → allows Ubuntu to manage SSL/TLS certificates. Necessary to download secure packages via HTTPS.
• curl → tool to download files from the internet via command line.
• gnupg → allows you to manage GPG keys, useful for verifying package authenticity.
• -y → automatic confirmation, avoids asking "Do you want to continue?".

**- Add Docker's GPG key**

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

• Creates the /etc/apt/keyrings folder with 0755 permissions (readable/executable by all, writable only by owner).
• Used to store the GPG key in a secure and standard location.

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
 | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
• Downloads Docker's public GPG key.
• --dearmor converts the key to a format readable by apt.
• Saves it to /etc/apt/keyrings/docker.gpg.

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg      # Ensures everyone can read the key (required by apt).
```

**3)Add the Docker repository**
```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
 https://download.docker.com/linux/ubuntu \
 $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
 | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
*	dpkg --print-architecture → restituisce la tua architettura (amd64, arm64, ecc.).
*	$(. /etc/os-release && echo $VERSION_CODENAME) → prende il nome della tua versione Ubuntu (es. focal, jammy).
*	Il tutto scrive in /etc/apt/sources.list.d/docker.list una riga che dice a Ubuntu da dove scaricare Docker.
*	signed-by=/etc/apt/keyrings/docker.gpg → specifica di usare la chiave appena scaricata.

**4)Install Docker**
```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
•	docker-ce → il “Docker Engine” vero e proprio.
•	docker-ce-cli → l’interfaccia a riga di comando (docker ...).
•	containerd.io → gestore dei container sottostante.
•	docker-buildx-plugin → estensione per build avanzate.
•	docker-compose-plugin → permette di usare docker compose
```bash
docker --version     #Mostra la versione installata di Docker 
```

**5)Optional - run Docker commands without "sudo"**
```bash
sudo groupadd docker           # crea il gruppo 'docker', se non esiste
sudo usermod -aG docker $USER  # aggiungi il tuo utente al gruppo
newgrp docker                  # applica subito i cambiamenti senza logout
docker run hello-world        #se funziona senza sudo, è tutto pronto.
```



