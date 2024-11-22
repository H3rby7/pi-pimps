# Cheatsheet

# Packages

## pacman

    # updates
    pacman -Syu

    # search
    pacman -Qa searchterm

    # install
    pacman -S your-pkg

    # uninstall / remove
    pacman -Rs your-pkg

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