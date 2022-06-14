# pi-pimps

(just keeping track of) Changes to pi:

# Content

* [Changes](#changes)
* [DynDNS](#dyndns)
* [Deployed Platform Services](#platform-services)

# Changes

1. added authorized_keys
2. [Improve SSH security](#ssh)
3. added pi-hole (https://github.com/pi-hole/pi-hole)
    1. `sudo vi /etc/lighttpd/external.conf` and add: `server.port := 8093` and reboot lighthttpd service `sudo /etc/init.d/lighttpd restart` (so pi-hole admin interface will run on PORT 8093 now)
4. `sudo apt-get install vim` (see https://askubuntu.com/questions/293407/ubuntu-terminal-is-not-working-properly-in-vi-editor)
5. Added https://wiki.debian.org/UnattendedUpgrades
    1. configured default
6. install [K3s](#k3s)
7. (optional, if FritzBox) [I made these changes to my FritzBox](./FritzBox.md)
8. configure 'private' manners (API keys etc.)

## SSH

Add/Uncomment in `/etc/ssh/sshd_config`.

    Protocol 2 #switch to SSH version 2
    Port XXX #change to different port to reduce noise
    PasswordAuthentication no #force use of keys instead of passwords
    PermitEmtpyPasswords no #no empty passwords allowed
    X11Forwarding no #no GUI display
    PermitRootLogin prohibit-password #root can't log in with password (also needs to use keys)
    
restart ssh demon `sudo systemctl restart sshd`
    

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
    
Copy custom manifests to k3s auto-deploy directory. Can repeat the `cp -rf` command on changes to reflect them in that auto-deploy directory (For example after pulling an update from this repo).

    sudo mkdir -p /var/lib/rancher/k3s/server/manifests
    sudo cp -rf ./var/lib/rancher/k3s/server/manifests/custom/* /var/lib/rancher/k3s/server/manifests/custom/

https://rancher.com/docs/k3s/latest/en/installation/install-options/#options-for-installation-with-script

    export K3S_TOKEN=XXXXXXXXXXXXXXXXX-XXXXXXXXXXXXXX-XXXXXXXXXXXXXX
    curl -sfL https://get.k3s.io | sh -

After installation check with `sudo kubectl version`.

## DynDNS

Running DynDNS on the Pi itself, to get IPV6 working properly. I chose DuckDNS.

Created an own shell script at `~/duckdns/duck.sh` to run with crontab.
Started with [DuckDNS Sample](https://www.duckdns.org/install.jsp?tab=linux-cron) and added IPV6 support.

```sh
ipv6=$(curl http://ip1.dynupdate6.no-ip.com/)
echo url="https://www.duckdns.org/update?domains=mydomain&token=your-token-from-duck-dns&ipv6=${ipv6}&ip=" | curl -k -o ~/duckdns/duck.log -K -
```

My crontab runs every 10 minutes (not 5), to not get banned by the IPV6 service.

    crontab -e

    */10 * * * * ~/duckdns/duck.sh >/dev/null 2>&1

## Let's Encrypt for TLS

Copy the templates

    cp -r templates/* private/
    
And fill in your own data/keys/etc.

Create necessary secrets:
    
    kubectl -ncert-manager create secret generic issuer-account-key --from-literal=tls.key=YOURTLSKEY

# Platform Services

## Traefik

Description | Port | Protect from Internet Traffic?
--- | --- | ---
HTTP to HTTPS redirect | [30080](http://raspberrypi:30080) | no
Traefik Ingress Routing | [30443](http://raspberrypi:30443) | no
Traefik Dashboard | [30900](http://raspberrypi:30900) | Definitely YES!

# Cheatsheet

Useful commands to interact with K3S

Command | Purpose
--- | ---
`k3s-killall.sh` | Provided by K3S binaries, stops K3S service and the K3S Kubernetes Cluster
`k3s-uninstall.sh` | Provided by K3S binaires, completely uninstall K3S from the system (also clears all `*/rancher/k3s/*` directories, so make sure to have backups of those files if necessary.
`curl -sfL https://get.k3s.io \| INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_FORCE_RESTART=true sh -` | force K3S (Service) Restart
