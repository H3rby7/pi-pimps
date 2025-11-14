# Pihole + Kodi

## PiHole

    journalctl -u pihole-FTL

One-Step automated install will not work. -> https://wiki.archlinux.org/title/Pi-hole

A Arch User Repository (AUR) version is available. Generic AUR install guide -> https://wiki.archlinux.org/title/Arch_User_Repository

    sudo pacman -S base-devel openbsd-netcat

Install Pi-Hole FTL (AUR dependency of Pi-Hole Server)

(possibly easier with pamac, especially when having added the [arch4edu repo](../README.md#arch4edu-pacman-repository)

    cd ~
    git clone https://aur.archlinux.org/pi-hole-ftl.git
    cd pi-hole-ftl
    # Inspect PKGBUILD
    less PKGBUILD
    # Run makepkg into an error to see the list of missing packages
    makepkg
    # Either install them manually or re-run with -sr
    makepkg -sr
    # Install the package
    sudo pacman -U pi-hole-ftl-X.XX.X-X-aarch64.pkg.tar.zst
    sudo systemctl restart pihole-FTL.service

[pihole-FTL.service likely fails](https://wiki.archlinux.org/title/Pi-hole#Failed_to_start_Pi-hole_FTLDNS_engine):

    # check
    systemctl status pihole-FTL.service
    # read logs
    journalctl -u pihole-FTL
    # If error is "port 53 is occupied"
    sudo mkdir /etc/systemd/resolved.conf.d
    sudo vim /etc/systemd/resolved.conf.d/pi-hole.conf

    [Resolve]
    DNSStubListener=no

    sudo systemctl restart systemd-resolved.service pihole-FTL.service


Install Pi-Hole Server

(possibly easier with pamac)

    cd ~
    git clone https://aur.archlinux.org/pi-hole-server.git
    cd pi-hole-server
    # Inspect PKGBUILD
    less PKGBUILD
    # Run makepkg into an error to see the list of missing packages
    makepkg
    # Either install them manually or re-run with -sr
    makepkg -sr
    # Install the package
    sudo pacman -U pi-hole-server-X.XX.X-X-any.pkg.tar.zst

### PiHole Configuration

    sudo pacman -S php-sqlite lighttpd php-cgi

Enable some extensions (all around 1k lines into the file)

    vim /etc/php/php.ini

    [...]
    extension=pdo_sqlite
    [...]
    extension=sockets
    [...]
    extension=sqlite3
    [...]

Limit php file access

    vim /etc/php/php.ini

    [...]
    open_basedir = /srv/http/pihole:/run/pihole-ftl/pihole-FTL.port:/run/log/pihole/pihole.log:/run/log/pihole-ftl/pihole-FTL.log:/etc/pihole:/etc/hosts:/etc/hostname:/etc/dnsmasq.d/02-pihole-dhcp.conf:/etc/dnsmasq.d/03-pihole-wildcard.conf:/etc/dnsmasq.d/04-pihole-static-dhcp.conf:/var/log/lighttpd/error-pihole.log:/proc/loadavg:/proc/meminfo:/proc/cpuinfo:/sys/class/thermal/thermal_zone0/temp:/tmp
    [...]

Copy lighttpd config of pi-hole (may need to adjust later to support kodi WEB)

    cp /usr/share/pihole/configs/lighttpd.example.conf /etc/lighttpd/lighttpd.conf

Enable and start lighttpd service

    sudo systemctl enable lighttpd.service
    sudo systemctl start lighttpd.service

should now be able to CURL the page.

    curl localhost/admin/
    curl http://raspberrypi/admin/
    curl http://XXX.XXX.XXX:80/admin/

Update hosts file

    sudo vim /etc/hosts
    127.0.0.1          localhost
    <XXX.XXX.XXX.XXX>  pi.hole <yourhostname>

Pi-Hole should now be running

### Make PiHole your DNS

https://docs.pi-hole.net/routers/asus/

    # Shows IPV4 and IPV6 address
    iwctl station wlan0 show

## KODI

https://wiki.archlinux.org/title/Kodi

https://kodi.wiki/view/HOW-TO:Install_Kodi_on_Raspberry_Pi

    journalctl -u kodi.service
    # LOGs of current run
    sudo tail -f /var/lib/kodi/.kodi/temp/kodi.log
    # LOGs of last run
    sudo less /var/lib/kodi/.kodi/temp/kodi.old.log

### KODI installation

https://manjaristas.org/branch_compare?q=kodi-rpi&arm=on

    sudo pacman -S kodi-rpi
    sudo vim /etc/systemd/system/kodi.service

Major Version: Kodi-Nexus 20.5

```plain
[Unit]
Description=Kodi standalone service
After=network.target

[Service]
User=kodi
Group=kodi
Type=simple
ExecStart=/usr/bin/kodi-standalone
Restart=on-failure
RestartSec=5
StandardOutput=null
StandardError=null

[Install]
WantedBy=multi-user.target
```

    sudo systemctl daemon-reload
    sudo systemctl enable kodi.service

## Switch to testing branch

    # This did not fully work:
    sudo pacman -U https://ftp.tu-chemnitz.de/pub/linux/manjaro/arm-testing/extra/aarch64/kodi-rpi-21.2-15-aarch64.pkg.tar.zst

    # Switched to pacman 'testing' branch:
    sudo pacman -S kodi-rpi libcdio libdisplay-info libnfs libxml2 libplacebo

    # Stuck at 'libfmt.so.11'
    # Replace libcec-rpi with libcec
    sudo pacman -S libcec

## Switch back to stable branch

Downgrade all packages back to version of `stable`:

    sudo pacman -Syyuu
