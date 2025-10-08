# Raspberrypi

## Cooling using a Fan via GPIO

Either use a FAN with built-in GPIO control and connect it
OR build your own setup with a transistor.

Add the following line to `/boot/config.txt`:

    dtoverlay=gpio-fan,temp=60000

*NOTE: Uses GPIO12 by default*

    less /boot/overlays/README

### Measure Temperature:

    vcgencmd measure_temp

## Daily Reboot

Add config files:

`/etc/systemd/system/sched-reboot.service`

```conf
[Unit]
Description=Reboot in 5 Minutes

[Service]
Type=oneshot
ExecStart=/sbin/shutdown -r +5
```

`/etc/systemd/system/sched-reboot.timer`

```conf
[Unit]
Description=Reboot Scheduling to 4 AM

[Timer]
OnCalendar=*-*-* 4:00:00

[Install]
WantedBy=multi-user.target
```

```bash
# Check validity (no return values is good)
systemd-analyze verify /etc/systemd/system/sched-reboot.*

# Enable Timer
sudo systemctl enable sched-reboot.timer

# Start Timer
sudo systemctl start sched-reboot.timer

# Check Timer shows
systemctl list-timers
```
