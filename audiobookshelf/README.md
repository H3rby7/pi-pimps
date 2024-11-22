# Audiobookshelf

https://www.audiobookshelf.org/

https://www.audiobookshelf.org/docs

https://aur.archlinux.org/packages/audiobookshelf

No support for AARCH64 through:

    pamac build audiobookshelf

First we need the correct npm version

Find the package `node_modules/cssnano` within https://github.com/advplyr/audiobookshelf/blob/master/client/package-lock.json and check for the `engines` attribute. E.g. `20.18.1`

For convenience we can use a tool to switch/install different node versions:

    pamac build nvm
    echo 'source /usr/share/nvm/init-nvm.sh' >> ~/.bashrc
    # Restart your shell
    logout
    nvm install 20.18.1

Then try to build it myself:

    git clone https://aur.archlinux.org/audiobookshelf.git
    cd audiobookshelf

CHANGE PKGBUILD

    vim PKGBUILD

    >> remove 'npm' and 'nodejd' von makedepends (as the pre-check does not work with 'nvm')
    >> change -t node20-linux-x64 to node20-linux-arm64

Run makepkg into an error to see the list of missing packages

    makepkg -Asr
    # Will error on the checksum for 'audiobookshelf', so we update checksums
    updpkgsums
    # Re-Run
    makepkg -Asr
    # Install the package
    sudo pacman -U audiobookshelf-v2.17.2-1-aarch64.pkg.tar.zst

Enable and start the service

    systemctl enable audiobookshelf.service
    systemctl start audiobookshelf.service

Visit http://your.pi.ip.addr:13378
