# Einrichten einer usb0 Netzwerk-Schnittstelle für den Raspberry PI
um den raspbarry pi über die usb schnittstelle zu managen.

## Einstellungen auf der SD-Karte 
zur vorbereitung müssen zwei dateinen auf der SD-Karte angepasst werden 

### Anpassungen in der `config.txt`
Am ende/letzt zeile der Datei muss eingefügt werden `dtoverlay=dwc2`

### Anpassungen in der `cmdline.txt`
Hier muss nach `rootwait` der Wert `modules-load=dwc2,g_ether` eingetragen werden. Achtung es muss darauf geachtet werden das vor und nach dem Wert jeweils nur ein Leerzeichen stehen.


## IP Konfiguration au dem Raspbarry-PI
### DHCP server
Um später die suche nach den PI zu vermeiden kann ein DHCP server auf dem pi verwendet werden.
Zu empfehlen ist der von dnsmasq da dieser sehr einfach zu konfigurieren ist. wir installieren diesen mit `sudo apt-get install dnsmasq`.
Nach der installation muss die konfiguration angepasst werden.
`sudo nano /etc/dnsmasq.conf`

Wir Konfigurieren hier das **172.31.255.0/24** Netzwerk. Die Werte dafür sind:
```
interface=usb0
bind-dynamic
bogus-priv
dhcp-range=172.31.255.20,172.31.255.100,255.255.255.0,12h
```

### Statische IP-Adresse
Wir müssen uns auch eine statische IPv4-Adresse für unser 'usb0'-Interface vergeben. Dazu müssen wir wieder eine Konfiguration angepasst werden.
`sudo nano /etc/network/interfaces.d/usb0`

Wie vergeben uns in unserem **172.31.255.0/24** netzwerk die letzte ip adresse **172.31.255.254**. Die Werte dafür sind:
```
auto usb0
iface usb0 inet static
    address 172.31.255.254
    netmask 255.255.255.0
    dns-domain zeropi.lan
    dns-nameservers 1.1.1.1 8.8.8.8
```
Jetzt kann der dnsmasq-Service neugestartet werden: 
`sudo systemctl restart dnsmasq`

Wir prüfen ob alles funktioniert hat: 
`sudo systemctl status dnsmasq`

Um sicher zu stellen das der Service auch beim Sytemstart ausgeführt wird: 
`sudo systemctl enable dnsmasq`


## Windows Treiber
