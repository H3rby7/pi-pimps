# Pi setups

(just keeping track of) Changes to pi:

As from time to time a pi is reset and built anew ;)

[RaspberryPi 4 Model B](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/specifications/)
* ARM v8
* 64-bit SoC @ 1.8GHz

* [K3s Kubernetes on Raspi](./k3s%20on%20pi/)
* [Pihole and Kodi](./pihole+kodi/)

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