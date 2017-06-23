# Raspberry PI 3 Model B Access Point Configuration (without DHCP)
This documents the steps needed to configure a Raspberry PI 3 Model B as an WiFi access point.  The main purpose for this document is mainly for myself so that I remember what needs to be done in the case of I need to reconfigure this access point.

I found that a lot of online document only describe the steps needed when you want to configure your Pi as an guest WiFi access point, meaning the access point also act as a DHCP server.

My use case for this access point is that my router would be the DHCP server and the Pi would only provide access point functionality.

This Raspberry Pi is installed with Raspbian Jessie Lite.

1. Update your Raspbian installation
```
sudo apt-get update
sudo apt-get dist-upgrade
```

2. Install hostapd and bridge-utils
```
sudo apt-get install hostapd bridge-utils
```
3. Stop hostapd since it is not yet ready to run
```
sudo systemctl stop hostapd
```

4. Set DAEMON_CONF variable in ```/etc/default/hostapd```
```
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```

5. Create /etc/hostapd/hostapd.conf
```
interface=wlan0
driver=nl80211
hw_mode=g
channel=6
ieee80211n=1
wmm_enabled=1
ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP

ssid=set_your_ssid_here
wpa_passphrase=set_your_password_here

bridge=br0
```

6. Update ```/etc/network/interfaces```
```
# interfaces(5) file used by ifup(8) and ifdown(8)

# Please note that this file is written to be used with dhcpcd
# For static IP, consult /etc/dhcpcd.conf and 'man dhcpcd.conf'

# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d

auto lo
iface lo inet loopback

# Disable eth0 / wlan0 config, handled by bridge
# iface eth0 inet dhcp
auto eth0
iface eth0 inet manual

allow-hotplug wlan0
iface wlan0 inet manual
#   wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

# Create a bridge using DHCP
auto br0
iface br0 inet dhcp
bridge_ports eth0 wlan0
pre-up ifconfig eth0 0.0.0.0 up
pre-up ifconfig wlan0 0.0.0.0 up
pre-up brctl addbr br0
pre-up brctl addif br0 eth0
post-down ifconfig wlan0 0.0.0.0 down
post-down ifconfig eth0 0.0.0.0 down
post-down brctl delif br0 eth0
post-down brctl delbr br0
```

7. I had some issue with IPv6 on this configuration so I simply disable it by updating /etc/sysctl.conf.  What that means is no IPv6 addresses will be assigned to devices connected to this access point.
```
net.ipv6.conf.all.disable_ipv6=1
```

8. That should be it, now just reboot your Pi.

## Other Notes
### Unable to start hostapd via systemctl
Discovered while setting a 2nd access point that hostapd cannot be started via systemctl.  After looking into it, apparently in ```/etc/init.d/hostapd```, there's a line that sources the ```. /lib/lsb/init-functions``` and that command is failing.  Comment out that line in ```/etc/init.d/hostapd``` resolved the issue.
### Setting DHCP Server on wlan0 after bridge is already set
1. Install dnsmasq
```
sudo apt-get install dnsmasq
```
2. Update ```/etc/dhcpcd.conf``` and add the following line to the bottom of the file
```
denyinterfaces wlan0
```
3. Update ```/etc/network/interfaces```
```
allow-hotplug wlan0  
iface wlan0 inet static  
    address 192.168.9.1
    netmask 255.255.255.0
    network 192.168.9.0
    broadcast 192.168.9.255
```
4. Configure DNSMASQ ```/etc/dnsmasq.conf``` by copy and paste the following into the conf file
```
interface=wlan0      # Use interface wlan0  
listen-address=192.168.9.1 # Explicitly specify the address to listen on  
bind-interfaces      # Bind to the interface to make sure we aren't sending things elsewhere  
server=8.8.8.8       # Forward DNS requests to Google DNS  
domain-needed        # Don't forward short names  
bogus-priv           # Never forward addresses in the non-routed address spaces.  
dhcp-range=192.168.9.50,192.168.9.150,12h # Assign IP addresses between 192.168.9.50 and 192.168.9.150 with a 12 hour lease time
```
5. Update ```/etc/default/dnsmasq``` and set ``IGNORE_RESOLVCONF```
6. Update ```/etc/hostapd/hostapd.conf```
Comment out ```bridge=br0```
7. That should be it, now just reboot your Pi.

## References
[Using the Raspberry Pi as an Access Point to Share an Internet Connection](https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md)

[WiFi AccessPoint bridged to LAN transparently](https://raspberrypi.stackexchange.com/questions/14318/wifi-accesspoint-bridged-to-lan-transparently)
