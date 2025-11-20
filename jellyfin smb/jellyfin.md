# Jellyfin

Could skip reinstallation of jellyfin-optimized ffmpeg,
[because Pi HW Accelleration was dropped](https://jellyfin.org/docs/general/post-install/transcoding/hardware-acceleration/#raspberry-pi-hardware-acceleration-support-deprecation)

Jellyfin binaries have **hardcoded** jellyfin-ffmpeg dependency, which collides with the standard 'ffmpeg' on their dependencies.

## Installation

### Install portable

https://jellyfin.org/docs/general/installation/advanced/manual#portable-linux-install

    sudo mkdir -p /opt/jellyfin
    sudo cd /opt/jellyfin
    sudo curl -L -o jellyfin_10.11.2-arm64.tar.gz https://repo.jellyfin.org/files/server/linux/latest-stable/arm64/jellyfin_10.11.2-arm64.tar.gz
    sudo tar xvzf jellyfin_10.11.2-arm64.tar.gz
    sudo mkdir data cache config log

    sudo vim jellyfin.sh

```sh
#!/bin/bash
JELLYFINDIR="/opt/jellyfin"
FFMPEGDIR="/usr/bin" # note: this way it is not using the jellyfin-ffmpeg

$JELLYFINDIR/jellyfin/jellyfin \
 -d $JELLYFINDIR/data \
 -C $JELLYFINDIR/cache \
 -c $JELLYFINDIR/config \
 -l $JELLYFINDIR/log \
 --ffmpeg $FFMPEGDIR/ffmpeg
```

    sudo chown -R youruser:youruser *
    sudo chmod +x jellyfin.sh
    sudo vim /etc/systemd/system/jellyfin.service

```csharp
[Unit]
Description=Jellyfin
After=network.target

[Service]
Type=simple
User=youruser
Restart=always
ExecStart=/opt/jellyfin/jellyfin.sh

[Install]
WantedBy=multi-user.target
```

    sudo systemctl daemon-reload
    sudo systemctl enable jellyfin.service
    sudo systemctl start jellyfin.service

[Finish](#finish-installation)

### using pamac and pacman

    pamac install jellyfin-server-bin
    pacman -S jellyfin-web

    # Adjust Jellyfin-Web install path in
    sudo vim /etc/conf.d/jellyfin

    systemctl start jellyfin.service

[Finish](#finish-installation)

### From Source (requires docker...)

Hopefully resolves dependency conflicts after moving pacman to 'testing' branch.
Sadly, pamac build jellyfin-server did not succeed.
[So we build from source](https://jellyfin.org/docs/general/installation/advanced/source#linux-or-macos)

    git clone https://github.com/jellyfin/jellyfin-packaging.git
    cd jellyfin-packaging
    git reset v10.11.2-202511022129
    git submodule update --init

    # Build process uses python with requirements.txt so we to install its deps
    # here, manually via pacman, as we have no virtual env
    sudo pacman -S python-gitpython python-yaml python-packaging

    ./build.py --help

[Finish](#finish-installation)

## Finish Installation

Visit Web-UI for install wizard.

    http://<PI IP ADDR>:8096/web

## Updating

    pamac build jellyfin-server-bin
    systemctl restart jellyfin

## Playlist Handling

If the media is stored on an external drive, it may not be available (for one of many reasons) to jellyfin at all times.
Jellyfin playlists will automatically remove any missing media from themselves.

One approach to handle this behaviour is to edit 'tasks', namely the triggers fo 'clean up collections and playlists'. Click on that task and remove the 'on startup' trigger. May help. Or not.

# Cheatsheets

    journalctl -u jellyfin

    sudo systemctl daemon-reload
    sudo systemctl enable jellyfin.service
    sudo systemctl start jellyfin.service

# Read also

* https://wiki.archlinux.org/title/Jellyfin
