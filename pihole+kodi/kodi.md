# Kodi

https://kodi.wiki/view/Special_protocol

## Web Interface

* Enable Web Interface using the GUI (Settings > Services)

## Repositories

    curl -o repository.kodinerds-7.0.1.7.zip https://repo.kodinerds.net/addons/repository.kodinerds/repository.kodinerds-7.0.1.7.zip
    sudo mv repository.kodinerds-7.0.1.7.zip /var/lib/kodi/

Using the GUI Add-on browser, 'install from zip file' to install the kodinerds repository.

Then via kodinerds repository install 'Sandmann79s Repository'

## Amazon VOD

    Via Sandmann79s Repository

### Amazon VOD inputstream

Build myself -> https://github.com/xbmc/inputstream.adaptive/wiki/How-to-build

Try getting via ARCH extra repos

    sudo pacman -S pamac-cli
    pamac build kodi-nexus-addon-inputstream-adaptive-git
    Chose:
      1:  kodi-rpi-dev      20.5-3                   extra
      2:  kodi-rpi-git-dev  21.x.65444.c3011d70d0-1  extra
      -> 1

Via [Widewine installer](https://github.com/AsahiLinux/widevine-installer)

**Widewine for aarch64 requires glibc >= 2.36**

https://archlinuxarm.org/packages/aarch64/glibc

    # higher version of GLIBC
    curl -o glibc-X.XX-X-aarch64.pkg.tar.xz http://mirror.archlinuxarm.org/aarch64/core/glibc-X.XX-X-aarch64.pkg.tar.xz
    sudo pacman -U glibc-X.XX-X-aarch64.pkg.tar.xz

    # DEP of widevine installer
    sudo pacman -S squashfs-tools


    git clone https://github.com/AsahiLinux/widevine-installer
    cd widevine-installer
    sudo ./widevine-installer
     ... version 4.10.2662.3

InputStream Adaptive Change DRM Widevine Decrypter Path from `special://home/cdm` (old) to `/var/lib/widevine` (new).

    sudo reboot
