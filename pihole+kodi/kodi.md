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

## Netflix

https://github.com/CastagnaIT/plugin.video.netflix/wiki/How-install-the-addon

Add repository

    curl -L -o repository.castagnait-2.0.1.zip https://github.com/castagnait/repository.castagnait/raw/kodi/repository.castagnait-2.0.1.zip
    sudo mv repository.castagnait-2.0.1.zip /var/lib/kodi/

Using the GUI Add-on browser, 'install from zip file' to install the CastagnaIT Repository.

Using GUI Add-on browser, 'install from repository' select CastagnaIT Repository and install Netflix

https://github.com/CastagnaIT/plugin.video.netflix/wiki/Login-with-Authentication-key

Did encounter a playback error, that also affected Prime, after a restart everything turned out fine.

## Network files

(https://www.kodinerds.net/thread/74559-fritz-nas-und-smb/)

Settings > Media > Library > Videos/Music

Add X

New Source > Add new Network connection

    Type: SMB
    Server Name: 192.xxx.xxx.1
    user with READ priviledges

## Youtube

[Following this 'Guide'](https://github.com/anxdpanic/plugin.video.youtube/wiki/Personal-API-Keys)

[Create keys here](https://console.cloud.google.com/apis/credentials/wizard?api=youtube.googleapis.com)

    Youtube Data API v3
    Public Data
    restrict key to 'Youtube Data API v3'

    Consent Screen
    View and Download Videos

    Youtube Data API v3
    Private Data

    sudo systemctl stop kodi.service
    sudo vim /var/lib/kodi/.kodi/userdata/addon_data/plugin.video.youtube/api_keys.json
    sudo systemctl start kodi.service

## PVR

https://github.com/kodi-pvr/pvr.iptvsimple/tree/Matrix

Build ourselves, as its not showing up in kodi addons

    pamac build kodi-addon-pvr-iptvsimple

restart kodi to enable add-on

### PVR TV channels

Add a m3u list. For example:

    https://raw.githubusercontent.com/jnk22/kodinerds-iptv/master/iptv/clean/clean_tv.m3u

Takes some time, maybe a restart to apply.

Install Takealug EPG Grabber form Kodinerds > Services

    sudo mkdir /var/lib/kodi/epg-storage

Takealug EPG Grabber go to 'Glocal Settings' and select the newly created DIR as storage path

Then go to 'Misc' and do a manual EPG run.

    sudo ls -al /var/lib/kodi/epg-storage/

should now display guide.xml

### PVR Radio channels

m3u lists must use this tag : radio="true"

e.g.

    #EXTINF:-1,tvg-logo="http://path.to/logo.png" group-title="some guop" radio="true",Radio Name
    http://path.to.your/radiostation
