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

Given that you will have an SD card inside an SD card reader via USB.

Use the script below with your adjustments

VAR | how to find it
--- | ---
USER | `cat /etc/passwd` to find the user
USER_UID | `cat /etc/passwd` to find the corresponding user ID
SD_READER_MODEL | `lsblk -S` to find the reader, take the 'Model' column entry
DEVICE_UUID | `lsblk -f` to find the card, take the 'UUID' column entry

```bash
#!/bin/bash

USER="your-user"
USER_UID="1001"
GROUP_UID="$USER_UID"
FMASK="133" # Files will be -rw-r--r--
FS_TYPE="exfat"
SD_READER_MODEL="ZZZZ-ZZZZ"
DEVICE_UUID="XXXX-XXXX"
MOUNT_POINT="/run/media/$USER/$DEVICE_UUID"
DEVICE="/dev/disk/by-uuid/$DEVICE_UUID"

# Create mount point if it does not exist
if [ ! -d "$MOUNT_POINT" ]; then
    sudo mkdir -p "$MOUNT_POINT"
fi

for i in {1..10}; do
    sleep 3
    # Check if the card is already mounted
    if grep -qs "$MOUNT_POINT " /proc/mounts; then
        echo "SD Card $DEVICE is already mounted. Exiting"
        exit 0
    fi

    # Check for the SD Reader to be detected
    READER=`lsblk -S | grep $SD_READER_MODEL | wc -l`
    if [ $READER -ne 1 ]; then
        echo "Waiting for SD Card Reader $SD_READER_MODEL..."
        continue
    fi
    echo "SD Card Reader $SD_READER_MODEL found, attempting to find SD Card $DEVICE..."

    # Check for the block device (the SD card) to be present
    if [ ! -b "$DEVICE" ]; then
        echo "Waiting for SD Card $DEVICE..."
        continue
    fi

    # Mount the card
    echo "SD Card $DEVICE found, attempting to mount..."
    if mount -t $FS_TYPE -o uid=$USER_UID,gid=$GROUP_UID,fmask=$FMASK "$DEVICE" "$MOUNT_POINT"; then
        echo "SD Card $DEVICE successfully mounted. Exiting"
        exit 0
    fi
    echo "Error mounting $DEVICE, retrying..."
done

echo "Failed to mount $DEVICE after 10 retries"
exit 1
```

## Systemd Service

```csharp
[Unit]
Description=Automount SD card for 'your-user' on boot
After=local-fs.target

[Service]
Type=oneshot
ExecStart=/usr/bin/mount-sd-card-jellyfin
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```

### Why not mount via fstab?

Using a service and script allows us to have several attempts, ideally being ready really fast.

### Why not mount via systemd mounts?

Found it too late, but looks good for next time (something breaks) ->
https://www.freedesktop.org/software/systemd/man/latest/systemd.mount.html

# Some useful commands

    sudo fsck -Va /dev/sd??
    sudo fdisk -l
    dmesg
    lsblk -f
