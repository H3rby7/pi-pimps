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

### Inputstream Adaptive

While some solutions are presented below, for me, this combination worked:

* [Downloaded Inputstream.Adaptive](#download-inputstream-adaptive)
* [Downloaded Inputstream.Helper](https://github.com/emilsvennesson/script.module.inputstreamhelper/releases/tag/v0.8.4) - not sure if necessary, because I had to disable it over it's settings in kodi GUI
* [Widevine Installer](#widevine-installer)

#### Build Inputstream Adaptive

##### Build from Omega Sources:

Following https://github.com/xbmc/inputstream.adaptive/wiki/How-to-build

    # Create a working dir
    mkdir a-dir && cd a-dir

    # Pre-requisite, a cmake < 4
    curl -L -o cmake-3.31.9-linux-aarch64.sh https://github.com/Kitware/CMake/releases/download/v3.31.9/cmake-3.31.9-linux-aarch64.sh
    chmod +x cmake-3.31.9-linux-aarch64.sh

    ./cmake-3.31.9-linux-aarch64.sh
    # and install into subdir, (say 'y' when prompted)

Get source code

    git clone --revision=refs/tags/21.3-Omega https://github.com/xbmc/xbmc.git

    # version 21.5.9, because of: https://github.com/xbmc/inputstream.adaptive/issues/1865#issuecomment-3418211625
    git clone --revision=refs/tags/21.5.9-Omega https://github.com/xbmc/inputstream.adaptive.git

Build (using the installed cmake)

    mkdir inputstream.adaptive/build/
    cd inputstream.adaptive/build/
    ../../cmake-3.31.9-linux-aarch64/bin/cmake -DADDONS_TO_BUILD=inputstream.adaptive -DADDON_SRC_PREFIX=../.. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../../xbmc/addons -DPACKAGE_ZIP=1 ../../xbmc/cmake/addons
    make

Package

    # The build resides in the xbmc/addons DIR, so we navigate there:
    cd ../../xbmc/addons

    # ZIP the 'inputstream.adaptive' DIR and write the output to our working DIR
    zip -r ../../inputstream.adaptive-aarch64-21.5.9.zip inputstream.adaptive

    # Go back into our working DIR, which now contains the 'inputstream.adaptive-aarch64-21.5.9.zip' file.
    cd ../..

##### 20.5-3

Build via pacman -> https://github.com/xbmc/inputstream.adaptive/wiki/How-to-build

Try getting via ARCH extra repos

    sudo pacman -S pamac-cli
    pamac build kodi-nexus-addon-inputstream-adaptive-git
    Chose:
      1:  kodi-rpi-dev      20.5-3                   extra
      2:  kodi-rpi-git-dev  21.x.65444.c3011d70d0-1  extra
      -> 1

#### Download Inputstream Adaptive

Version 21.5.17

[Download](https://addons.libreelec.tv/12.0.0/ARMv8/aarch64/inputstream.adaptive/)

### Widevine via Inputstreamhelper

and https://github.com/emilsvennesson/script.module.inputstreamhelper/releases/tag/v0.8.4
and download widevine via inputstreamhelper 0.8.4.

InputStream Adaptive Change DRM Widevine Decrypter Path from `special://home/cdm` (old) to `/var/lib/kodi/widevine` (new).

This path resulted in the error:
`inputstream.adaptive: Initialize: Initialize: Failed to load library: /var/lib/kodi/widevine/libwidevinecdm.so: DT_RELR without GLIBC_ABI_DT_RELR dependency`

### Widevine-Installer

Via [Widewine installer](https://github.com/AsahiLinux/widevine-installer)

**Widewine for aarch64 requires glibc >= 2.36**

https://archlinuxarm.org/packages/aarch64/glibc

    # [MAYBE] needs a higher version of GLIBC, than available via pacman:
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
