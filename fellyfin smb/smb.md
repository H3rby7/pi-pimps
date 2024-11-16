# SMB Packages

for easy file adding in LAN

https://wiki.archlinux.org/title/Samba


    sudo vim /etc/samba/smb.conf
    testparm

    smbpasswd -a jellyfin

    systemctl enable smb
    systemctl start smb
    systemctl enable nmb
    systemctl start nmb

    smbclient -L <raspi-host-name> -U=jellyfin


## Properly discoverable for devices on local network

https://github.com/Netgear/wsdd2
https://manpages.ubuntu.com/manpages/oracular/man8/wsdd2.8.html


    # Windows > 10
    pamac build wsdd2

### LLMNR

Windows stuff, powered by both wsdd2 and systemd-resolved. 
We need to turn it off on one of these.

turn off on systemd-resolved:

    # Set LLMNR
    sudo vim /etc/systemd/resolved.conf
    ...
    [RESOLVE]
    ...
    LLMNR=no

    systemctl daemon-reload
    systemctl restart systemd-resolved

turn off on wsdd2:

    # Add '-w' to ExecStart command, because 'LLMNR' is already taken care of by pihole systemd-resolve
    sudo vim /usr/lib/systemd/system/wsdd2.service

in any case:

    systemctl daemon-reload
    systemctl enable wsdd2
    systemctl start wsdd2
    journalctl -u wsdd2
    # check for errs

## AVAHI

for non-windows

    # other devices
    sudo pacman -S avahi
    systemctl enable avahi-daemon.service
    systemctl start avahi-daemon.service
