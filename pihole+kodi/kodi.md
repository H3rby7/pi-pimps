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

### Amazon VOD via inputstream

    sudo pamac build kodi-nexus-addon-inputstream-adaptive-git
    sudo pacman -S nspr

My Addons > InputStream Helper > Settings > (Re)install Widevine

(Did not work - threw errors)
(2nd try Did not work - jsut crashed)

Rollback

    sudo pacman -Rs kodi-nexus-addon-inputstream-adaptive-git
    sudo pacman -Rs nspr

New Approach

    mkdir kodi-addon-inputstream-adaptive.git
    cd kodi-addon-inputstream-adaptive.git/
    curl -o PKGBUILD https://raw.githubusercontent.com/archlinux/svntogit-community/refs/heads/packages/kodi-addon-inputstream-adaptive/trunk/PKGBUILD
    makepkg -sr

(aarch64 unsupported)

    rm -r kodi-addon-inputstream-adaptive.git

Build myself -> https://github.com/xbmc/inputstream.adaptive/wiki/How-to-build

Try getting via ARCH extra repos

    sudo pacman -S pamac-cli
    pamac build kodi-nexus-addon-inputstream-adaptive-git
    Chose:
      1:  kodi-rpi-dev      20.5-3                   extra
      2:  kodi-rpi-git-dev  21.x.65444.c3011d70d0-1  extra
      -> 1

Try external libwidevinecdm.so

(https://github.com/emilsvennesson/script.module.inputstreamhelper/issues/530#issuecomment-1467793760)

    curl -o libwidevinecdm.so https://slyguy.uk/.decryptmodules/widevine/4.10.2557.0-linux-arm64.so
    chmod 744 libwidevinecdm.so
    sudo mv /var/lib/kodi/.kodi/cdm/libwidevinecdm.so /var/lib/kodi/.kodi/cdm/libwidevinecdm.so.old
    sudo cp libwidevinecdm.so /var/lib/kodi/.kodi/cdm/
    sudo chown kodi:kodi /var/lib/kodi/.kodi/cdm/libwidevinecdm.so

Rollback

    sudo rm /var/lib/kodi/.kodi/cdm/libwidevinecdm.so
    sudo mv /var/lib/kodi/.kodi/cdm/libwidevinecdm.so.old /var/lib/kodi/.kodi/cdm/libwidevinecdm.so

Via [Chromium Widevine](https://aur.archlinux.org/chromium-widevine.git)

    git clone https://aur.archlinux.org/chromium-widevine.git
    makepkg -sr

(not for aarch64)

Via [Widewine installer](https://github.com/AsahiLinux/widevine-installer)

Requires glibc >= 2.36

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

### Amazon VOD via browser

Maybe not even possible? -> https://github.com/Sandmann79/xbmc/issues/15#issuecomment-259607882

    sudo pacman -S xorg-server xorg-xinit
    sudo pacman -S openbox
    sudo pacman -S chromium

rolling back

    sudo pacman -Rs openbox chromium xorg-server xorg-xinit

### Experiment with kodi-rpi-git

Input Stream Adaptive making trouble, trying to use `kodi-rpi-git` version

    sudo pacman -Rs kodi-rpi
    sudo pacman -S kodi-rpi-git

Major Version: Kodi-Omega

also does not show inputstream.adaptive as add-on, going back