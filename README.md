# pi-pimps

(just keeping track of) Changes to pi:

# Content

* [Changes](#changes)
* [Deployed Platform Services](#platform-services)

# Changes

1. added authorized_keys
2. added pi-hole (https://github.com/pi-hole/pi-hole)
3. `sudo apt-get install vim` (see https://askubuntu.com/questions/293407/ubuntu-terminal-is-not-working-properly-in-vi-editor)
4. Added https://wiki.debian.org/UnattendedUpgrades
    1. configured default
5. install K3s(#k3s)

## K3S 

K3S networking features require iptables and do not work with nftables. Raspberry Pi OS (formerly Raspbian) defaults to using nftables instead of iptables. Follow the steps below to switch configure Buster to use legacy iptables:

    sudo iptables -F
    sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
    sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
    sudo reboot

Standard Raspberry Pi OS installations do not start with cgroups enabled. K3S needs cgroups to start the systemd service. cgroupscan be enabled by appending
`cgroup_memory=1 cgroup_enable=memory` to `/boot/cmdline.txt`.

    sudo reboot

### Install K3S via scripts

    git clone https://github.com/H3rby7/pi-pimps.git
    sudo mkdir -p /etc/rancher/k3s
    sudo cp ./pi-pimps/etc/rancher/k3s/config.yaml /etc/rancher/k3s/config.yaml
    sudo chmod 644 /etc/rancher/k3s/config.yaml
    
    sudo mkdir -p /var/lib/rancher/k3s/server/manifests
    sudo cp -r ./pi-pimps/var/lib/rancher/k3s/server/manifests/custom /var/lib/rancher/k3s/server/manifests/custom

https://rancher.com/docs/k3s/latest/en/installation/install-options/#options-for-installation-with-script

    export K3S_TOKEN=XXXXXXXXXXXXXXXXX-XXXXXXXXXXXXXX-XXXXXXXXXXXXXX
    curl -sfL https://get.k3s.io | sh -

After installation check with `sudo kubectl version`.

# Platform Services

## Traefik

Description | Port | Protect from Internet Traffic?
--- | --- | ---
HTTP to HTTPS redirect | [http://raspberrypi:30080](30080) | no
Traefik Ingress Routing | [http://raspberrypi:30443](30443) | no
Traefik Dashboard | [http://raspberrypi:30900](30900) | Definitely YES!

