# Migration from VM on host PC to Raspberry Pi 5

The handover was very quick and I recreated most things from scratch except for the file with the WireGuard keys which I transferred to the new server via FileZilla.

I archived and zipped the entire WireGuard configuration folder with the tar command and then transferred it with FileZilla, an SFTP software, to the Raspberry.


```bash
sudo tar -czvf wg_config_backup.tar.gz /etc/wireguard

-c        Create - creates a new archive.
-z        Gzip - compresses the archive with gzip. This makes the file smaller and gives it a .gz extension.
-v        Verbose - shows the list of files being added to the archive.
-f        File - specifies the output file name (must be the last option).
```

Subsequently I completed the configurations and verified with some tests that everything was working.
