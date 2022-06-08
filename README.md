# pi-pimps

(just keeping track of) Changes to pi:

1. added authorized_keys
2. added pi-hole (https://github.com/pi-hole/pi-hole)
3. `sudo apt-get install vim` (see https://askubuntu.com/questions/293407/ubuntu-terminal-is-not-working-properly-in-vi-editor)
4. Added https://wiki.debian.org/UnattendedUpgrades
    1. configured default
5. switch to iptables (k3s needs those)

## K3S networking features require iptables and do not work with nftables
Raspberry Pi OS (formerly Raspbian) defaults to using nftables instead of iptables. Follow the steps below to switch configure Buster to use legacy iptables:

sudo iptables -F
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
sudo reboot
