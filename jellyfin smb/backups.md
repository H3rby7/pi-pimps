# Backup to external Drive

Mounting external drive:

1. Connect external drive
2. Find it's device id via `lsblk`
3. `sudo mount /dev/sd[??] /mount/path/of/my-ext-drive/backup-dir`

*NOTE: Sometimes when connecting a new external drive the PI messes up the [SD card mount](./sd-card-mounting.md) and needs a reboot with both devices already attached.*

## BorgBackup

https://borgbackup.readthedocs.io/en/stable/quickstart.html

### Installation

With python dependenices:

    sudo pacman -S borg

Or standalone binary from https://borg.bauerj.eu/

    curl -L -o borg https://borg.bauerj.eu/bin/borg-1.4.1-arm64
    chmod +x ./borg
    # or for every user:
    chmod a+x ./borg

### Backing Up to ext drive

```sh
# Run once: create the repository

# using system package
sudo borg init -e none /mount/path/of/my-ext-drive/backup-dir
# using standalone binary
sudo ./borg init -e none /mount/path/of/my-ext-drive/backup-dir

# Use repository for the backup
# Using --files-cache=mtime,size to check for modification ans dize changes only.
# By default borg checks more properties, but that does not work well with a mounted SD card.

# using system package
sudo borg create --progress --list --stats --filter=AME --files-cache=mtime,size /mount/path/of/my-ext-drive/backup-dir::Initial /run/media/jellyfin/my-device
# using standalone binary
sudo ./borg create --progress --list --stats --filter=AME --files-cache=mtime,size /mount/path/of/my-ext-drive/backup-dir::Initial /run/media/jellyfin/my-device
```

## Kopia

https://kopia.io/docs/repositories/#local-or-network-attached-storage

* sadly encryption is enforced, so backup FS cannot also be used as mirror as files are encrypted
* supports incremental backups

### Installation

    curl -o kopia.tar.gz -L https://github.com/kopia/kopia/releases/download/v0.21.1/kopia-0.21.1-linux-arm64.tar.gz
    tar -xzf kopia.tar.gz
    sudo cp kopia-0.21.1-linux-arm64/kopia /usr/bin/kopia

### Backing Up to ext drive

```sh
# Run once: create the repository
mkdir -p /mount/path/of/my-ext-drive/backup-dir
kopia repository create filesystem --path=/mount/path/of/my-ext-drive/backup-dir

# Use repository for the backup
```

## Rsync

[Rsync](https://linux.die.net/man/1/rsync)
is a simple copy
[backup utility](https://wiki.archlinux.org/title/Rsync#As_a_backup_utility).

Example usage:

    sudo rsync -r -P --delete /run/media/jellyfin/my-device /mount/path/of/my-ext-drive/backup-dir
