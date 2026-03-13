# Pihole + Kodi

## PiHole

    journalctl -u pihole-FTL

One-Step automated install will not work, because manjaro (pacman) is not a supported package manger.
-> https://wiki.archlinux.org/title/Pi-hole

An "Arch User Repository" (AUR) version is available.
Generic AUR install guide -> https://wiki.archlinux.org/title/Arch_User_Repository

Install some dependencies:

    sudo pacman -S base-devel openbsd-netcat

Install Pi-Hole FTL (AUR dependency of Pi-Hole Server)

(Easy with pamac, especially when having added the [arch4edu repo](../README.md#arch4edu-pacman-repository)

    cd ~
    pamac build pi-hole-ftl pi-hole-web

Create 

    sudo systemctl restart pihole-FTL

[pihole-FTL likely fails](https://wiki.archlinux.org/title/Pi-hole#Failed_to_start_Pi-hole_FTLDNS_engine):

    # check
    systemctl status pihole-FTL
    # read logs
    journalctl -u pihole-FTL
    # If error is "port 53 is occupied"
    sudo mkdir /etc/systemd/resolved.conf.d
    sudo vim /etc/systemd/resolved.conf.d/pi-hole.conf

    [Resolve]
    DNSStubListener=no

    sudo systemctl restart systemd-resolved.service pihole-FTL

### PiHole Configuration

By default the admin webserver uses port 8080, however we want that port to be available for the kodi webserver.
So we switch pihole webserver port to 9000

    sudo vim /etc/pihole/pihole.toml

```toml
[webserver]
  port = "9000,[::]:9000"
```

    sudo systemctl restart pihole-FTL

should now be able to CURL the page.

    curl localhost:9000/admin/
    curl http://raspberrypi:9000/admin/
    curl http://XXX.XXX.XXX:9000/admin/

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
    sudo systemctl start kodi.service

## Switch to testing branch

NEW:

    # kodi rpi 21.2-15 is installed with branch switch and update.
    sudo pacman -S libcec
