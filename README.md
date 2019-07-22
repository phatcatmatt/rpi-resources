# rpi-resources

## first time set up:

1. Login in with pi/raspberry
2. sudo raspi-config and change password, locale (deselect GB and select en-us), keyboard to genreic 105 key, English US
3. Sudo adduser [username] (adds user)
4. Sudo adduser [username] sudo (adds to sudo group)
5. Reboot
6. Login with new user
 - if bash history isn't working `sudo chsh -s /bin/bash <username>` https://askubuntu.com/questions/325807/arrow-keys-tab-complete-not-working
7. Sudo deluser pi
8. Connect with wifi via sudo raspi-config or edit wpa-supplicant
9. Sudo apt-get update
9. Sudo apt-get upgrade


## security 
https://makezine.com/2017/09/07/secure-your-raspberry-pi-against-attackers/

To add new user use `ADDUSER`:
 - https://www.raspberrypi.org/documentation/configuration/security.md

Ssh security and config options: 
 - https://ubuntuforums.org/showthread.php?t=831372

View ssh attempts and logs: 
 - http://kamilslab.com/2017/01/16/how-to-view-all-failed-login-ssh-attempts-on-the-raspberry-pi/
 

### network stuff
wpa_supplicant to set up wifi
```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

network={
        ssid="networkname"
        psk="networkpassword"
}
```

dhcpcd.conf to set up static IP for different interfaces
```
sudo nano /etc/dhcpcd.conf

interface wlan0
static ip_address=192.168.220.1/24
static routers=192.168.220.0
```


iptables examples
```
sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE  
sudo iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT  
sudo iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT 
```
Then make them persist on reboot by saving them
`sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"`


dnsmasq lets you turn your pi into a router and hand out ip addresses on a specific interface
```
sudo nano /etc/dnsmasq.conf

interface=eth0       # Use interface eth0  
listen-address=192.168.220.1   # Specify the address to listen on  
bind-interfaces      # Bind to the interface
server=8.8.8.8       # Use Google DNS  
domain-needed        # Don't forward short names  
bogus-priv           # Drop the non-routed address spaces.  
dhcp-range=192.168.220.50,192.168.220.150,12h # IP range and lease time 
```

### pivpn
> To change the hostname or IP address, you will need to change /etc/openvpn/easy-rsa/pki/Default.txt and your .ovpn files if you have already generated them.

#### links

running scripts on startup:
 - https://learn.sparkfun.com/tutorials/how-to-run-a-raspberry-pi-program-on-startup/all
 - https://www.raspberrypi.org/forums/viewtopic.php?f=91&t=131760

Watchdog to reset a frozen/locked pi automatically
 - https://raspberrypi.stackexchange.com/questions/1401/how-do-i-hard-reset-a-raspberry-pi

Email on pi
 - http://www.madirish.net/565
