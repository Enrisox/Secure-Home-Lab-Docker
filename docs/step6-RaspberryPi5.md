# Passaggio da VM su PC host a Raspberry pi5 

Il passaggio di consegne è stato molto rapido e ho ricreato la maggior parte delle cose da zero tranne che il file con le chiavi di Wireguard che ho passato al nuovo server tramite Filezilla


Ho Archiviato e zippato con comando tar l'intera cartella di configurazione di WireGuard e l'ho poi trasferita con Filezilla, software di SFTP, nel raspberry.

```bash
sudo tar -czvf wg_config_backup.tar.gz /etc/wireguard

-c        Create - crea un nuovo archivio.
-z        Gzip - comprime l'archivio con gzip. Questo rende il file più piccolo e lo fa finire in .gz.
-v        Verbose - mostra l'elenco dei file che vengono aggiunti all'archivio.
-f        File - specifica il nome del file di output (deve essere l'ultima opzione).
```

Successivamente ho completato le configurazioni e verificato con alcuni test che tutto fosse funzionante.
