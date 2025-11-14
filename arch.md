# Cheatsheet

# Packages

## pacman

    # updates
    pacman -Syu

    # search locally
    pacman -Q searchterm
    pacman -Qi searchterm

    # search "remote"
    pacman -F searchterm

    # install
    pacman -S your-pkg

    # uninstall / remove
    pacman -Rs your-pkg

    # Switch branch to stable/testing/unstable
    # First, make sure current branch's packages are up to date:
    sudo pacman -Syu
    # Then switch
    sudo pacman-mirrors --api --country ACountry --set-branch stable/testing/unstable
    # Then update.
    sudo pacman -Syu
    # May need to update/install python modules, so pacman-mirrors works again...
    sudo pacman -S python pacman-mirrors openssl python-requests python-urllib3 python-idna

    # Fully reset mirror list
    pacman-mirrors --country all --api --protocols all --set-branch stable

    # Manual intervention: https://archlinux.org/news/linux-firmware-2025061312fe085f-5-upgrade-requires-manual-intervention/
    pacman -Rdd linux-firmware
    pacman -Syu linux-firmware

    # Update
    pacman -Syu

## pamac

For AUR (user repository)

    # search
    pamac search -a searchterm

    # install / build
    pamac build your-pkg

# Net Utils

    netstat --> ss
    netstat -r --> ip route
    netstat -i --> ip -s link
    netstat -g --> ip maddr.

    ss -tuna
    sudo ss -tulpn | grep :5353