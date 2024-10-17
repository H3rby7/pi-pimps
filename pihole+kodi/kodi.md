# Kodi

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

    https://aur.archlinux.org/packages/kodi-nexus-addon-inputstream-adaptive-git
    makepkg -sr
      1) kodi-rpi-dev  2) kodi-rpi-git-dev -> 1
    sudo pacman -U kodi-nexus-addon-inputstream-adaptive-git-XX.XXX-X-aarch64.pkg.tar.zst
    sudo pacman -S nspr

Launch Amazon VOD and try launch a video, accept to install Widevine

(Did not work - threw errors)

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