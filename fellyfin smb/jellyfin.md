# Jellyfin

    pamac install jellyfin-server-bin
    pacman -S jellyfin-web

    # Adjust Jellyfin-Web install path in
    sudo vim /etc/conf.d/jellyfin

    systemctl start jellyfin.service

    journalctl -u jellyfin

Visit Web-UI for install wizard.

    http://<PI IP ADDR>:8096/web
