# Pihole + Kodi

(just keeping track of) Changes to pi:

## Distro Quick-Ref

Base: Manjaro ARM minimal

    systemctl
    journalctl


## Distro Installation

Use [Manjaro-ARM-minimal-rpi4](https://github.com/manjaro-arm/rpi4-images/releases/)
with RaspberryPi imager (would loved to use arch, however installation of arch onto SD card is a heckmeck when all you have is a windows desktop)

In RaspberryPI Imager advanced settings are all ignored
* user+password
* wifi ssid+password
* ssh key

### Distro Finish Install

After boot you need to:

* select keyboard layout
* create new user+password
* change root password
* select locale
* select timezone

in order to finish the setup.

### Distro connect to WIFI

pre-installed CLI tool -> https://wiki.archlinux.org/title/Iwd

    iwctl --passphrase <passphrase> <wlan0> connect <SSID>

or step by step

    iwctl
    device list
    station <wlan0> scan
    station <wlan0> get-networks
    station <wlan0> connect <SSID>
    exit

Check known networks

    iwctl station <wlan0> show

If connected continue

### Distro SSH access

on the PI create ~.shh DIR

    cd ~
    mkdir .ssh

Copy your public key:

    scp your-key.pub user@hostname:~/.ssh/authorized_keys

On your pi chmod 0700

    chmod 0700 your-key.pub

Check if you can login from your machine via ssh

    ssh -i .ssh\your-key user@hostname

### Install updates

    sudo pacman -Syu
    ...
    Replace raspberrypi-bootloader-x with core/raspberrypi-bootloader? [Y/n] -> Y
    Replace rpi-eeprom with extra/rpi4-eeprom? [Y/n] -> Y
    dbus-units
      1) dbus-broker-units  2) dbus-daemon-units -> 2
    CONFLICT: raspberrypi-utils and raspberrypi-userland-aarch64-git (raspberrypi-firmware). remove "raspberrypi-userland-aarch64-git"? -> Y

### Install Useful Packages

    sudo pacman -S vim git

### Automatic updates

TODO: however not recommended for pacman

## PiHole

    journalctl -u pihole-FTL

One-Step automated install will not work. -> https://wiki.archlinux.org/title/Pi-hole

A Arch User Repository (AUR) version is available. Generic AUR install guide -> https://wiki.archlinux.org/title/Arch_User_Repository

    sudo pacman -S base-devel openbsd-netcat

Install Pi-Hole FTL (AUR dependency of Pi-Hole Server)

(possibly easier with pamac)

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

    journalctl -u kodi.service
    # LOGs of current run
    sudo tail -f /var/lib/kodi/.kodi/temp/kodi.log
    # LOGs of last run
    sudo less /var/lib/kodi/.kodi/temp/kodi.old.log

### KODI installation

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
Restart=always
RestartSec=5
StandardOutput=null
StandardError=null

[Install]
WantedBy=multi-user.target
```

    sudo systemctl daemon-reload
    sudo systemctl enable kodi.service
