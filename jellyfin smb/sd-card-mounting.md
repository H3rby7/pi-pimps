# Auto-Mount SD Card to pi

## Disable Kodi auto-mount

Kodi will attempt to mount media drives, if udisks or udisks2 is present:

https://kodi.wiki/view/Advancedsettings.xml#handlemounting


    sudo vim /usr/share/kodi/system/advancedsettings.xml

```xml
<advancedsettings version="1.0">
    ...
    <handlemounting>False</handlemounting>
    ...
</advancedsettings>
```

## Mount script

```bash
#!/bin/bash

USER="your-user"
USER_UID="1001"
GROUP_UID="$USER_UID"
FMASK="133" #Files will be -rw-r--r--
FS_TYPE="exfat"
DEVICE_UUID="XXXX-XXXX"
MOUNT_POINT="/run/media/$USER/$DEVICE_UUID"
DEVICE="/dev/sdX1"

# Create mount point if not exists
if [ ! -d "$MOUNT_POINT" ]; then
    sudo mkdir -p "$MOUNT_POINT"
fi

# Mount the device
sudo mount -t $FS_TYPE -o uid=$USER_UID,gid=$GROUP_UID,fmask=$FMASK "$DEVICE" "$MOUNT_POINT"
```

## Systemd Service

```csharp
[Unit]
Description=Automount SD card (/dev/sda1) for 'your-user' on boot
After=local-fs.target

[Service]
Type=oneshot
ExecStart=/usr/bin/mount-sda-jellyfin
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```

# Some useful commands

    sudo fsck -Va /dev/sda1
    sudo fdisk -l
    dmesg
    lsblk -f
