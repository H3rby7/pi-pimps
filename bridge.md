# Bride Wifi / Ethernet

## Notes

* iwctl configures WIFI/wireless only
* eth0 is avtually called eno0 in manjaro arm
* When accessing the pi via WIFI, plugging in an eth cable will change the default network interface of the pi to the cable and the pi will no longer be available via WIFI.
* Network managing service is: https://wiki.archlinux.org/title/Systemd-networkd

## Approach: PROXY ARP WITH SUBNETTING

Result: could not even ping the default gateway, although IP addrs and subnets were correct.

Source: https://raspberrypi.stackexchange.com/questions/88954/workaround-for-a-wifi-bridge-on-a-raspberry-pi-with-proxy-arp/88955#88955

### Systemd networkd files

within `/etc/systemd/network`

`08-wifi.network`

```sh
[Match]
Name=wl*

[Network]
DHCP=yes
IPForward=ipv4
IPv4ProxyARP=yes
```

`04-wired.network`

```sh
[Match]
Name=e*

[Network]
# Smallest subnet (2 ip addresses)
Address=XXX.XXX.XXX.253/30
DHCPServer=yes

[DHCPServer]
DNS=<router-ip>
```

### Make Wifi promiscuous

This way the wifi card forwards all traffic into the OS 
(which means also traffic for devices behind e*).

Create a service to hold `ip link set dev wlan0 promisc on`

`/etc/systemd/system/promiscuous-wifi.service`

```sh
[Unit]
Description=Set wlan0 interface to promiscuous mode, required for proxy ARP subnet - forward wifi to eth
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/bin/ip link set dev wlan0 promisc on
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

    sudo systemctl enable promiscuous-wifi
    
Source: https://wiki.archlinux.org/title/Network_configuration#Promiscuous_mode

### On the device(s) being e*

Manually specify DNS server to the pi address
