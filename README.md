# Thunderbolt Node to Node/P2P Network on Proxmox 8.0.4


LOAD THE MODULES AT BOOT 
```
nano /etc/modules
```
```
thunderbolt
thunderbolt-net
```

RESTART THE NODE

GET THE PCI PATH FOR EACH NODE - Use those in the 70-thunderbolt0.link
```
udevadm info /sys/class/net/thunderbolt0 | grep ID_PATH
```

CREATE LINK for the ETHERNET DEVICE for EACH NODE and use the PCI PATH ON EACH NODE Config.
```
nano /etc/systemd/network/70-thunderbolt0.link
```
NODE1
```
[Match]
Path=pci-0000:00:0d.3
Driver=thunderbolt-net
[Link]
MACAddressPolicy=none
Name=en05
```
NODE2
```
[Match]
Path=pci-0000:00:0d.3
Driver=thunderbolt-net
[Link]
MACAddressPolicy=none
Name=en05
```

RESTART THE NODES

NOW YOU CAN SEE THE NIC's en05 in PROXMOX

CONFIGURE A BRIDGE TO THE INTERFACES ON PROXMOX ON EACH NODE AND ASSIGN AN IP


****** THIS SEEMS TO BE NEEDED ATM OTHERWISE THERE IS NO CONNECTION AFTER A REBOOT ****

ADD NETWORK SERVICE RESTART ON EACH NODE
```
nano /usr/bin/network-rs.sh
```
```
# /bin/sh

sleep 20
systemctl restart networking.service
```

MAKE IT EXECUTABLE
```
chmod +x /usr/bin/network-rs.sh
```

RUN IT AT BOOT
```
crontab -e
```
ADD THE FOLLOWING LINE
```
@reboot  /usr/bin/network-rs.sh
```
