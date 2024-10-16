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

### Amazon VOD via browser

Maybe not even possible? -> https://github.com/Sandmann79/xbmc/issues/15#issuecomment-259607882

    sudo pacman -S xorg-server xorg-xinit
    sudo pacman -S openbox
    sudo pacman -S chromium
