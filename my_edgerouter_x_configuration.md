# Overview
This is a living document to document the process to configure EdgeRouter X on my home network.

The following is general information about my ISP and network devices I have.
* Internet service provider is Comcast
* ARRIS SURFboard SBG6700AC DOCSIS 3.0 Cable Modem/ Wi-Fi AC1600 Router
* EdgeRouter X with Firmware v1.9.1
* Netgear ProSAFE GS108T 8 Port Gigabit Smart Switch (GS108T-200NAS)
* TP-Link 5 Port Gigabit Unmanaged Switch (TL-SG1005D)
* TP-Link 8 Port Gigabit Unmanaged Switch (TL-SG1008D)
* Belkin AC 1750 DB Wi-Fi Dual-Band AC+ Gigabit Router (F9K1115)
* TP-Link N600 Wireless Wi-Fi Dual Band Router (TL-WDR3600)

I plan to create 3 VLANs on my home network, VLAN10, VLAN20 and VLAN30.
* VLAN10 consists of general accessible services such as NAS and printers
* VLAN20 consists of entertainment network
* VLAN30 consists of work network
* VLAN10 would have bi-directional communication with VLAN20 and VLAN30
* VLAN20 and VLAN30 cannot communicate with each other

## ARRIS SURFboard SBG6700AC DOCSIS 3.0 Cable Modem/ Wi-Fi AC1600 Router
Because I will be using the EdgeRouter X as the primary router, therefore we will disable the routing capability provided by this modem.  That also means I will lose the AC1600 WiFi.

**NOTE** According to documentation, although the SBG6700 does not route traffic when Bridge Mode is enabled, it can still act as a firewall if the firewall settings in the SBG6700 are enabled.

1. Access to the SBG6700AC web administration site
1. Basic -> Backup and Restore: Create a backup of your current configuration
1. Write down anything that might be important for you such as static IP addresses
1. Basic -> Setup: Set Gateway Mode from **Routed** to **Bridged**
1. Click the **Apply** button to restart SBG6700AC
**NOTE** To gain access back to the SBG6700AC web administration site, you will need to use the last IP address that SBG6700AC is assigned to.  In addition you might need to set a static IP address on your PC to match the subnet of SBG6700AC's IP address.

## EdgeRouter X with Firmware v1.9.1
EdgeRouter X will provide all the routing functionality of my network including receiving the public IP provided by Comcast.  Comcast provides both IPv4 and IPv6 addresses so it is necessary to take that into consideration when configuring the EdgeRouter X.

* Use WAN+2LAN2 Wizard

## Quick note on enabling IPv6 on VLANs
```
configure

set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.10
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.10 host-address ::1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.10 prefix-id 2
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.10 service slaac

set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.20
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.20 host-address ::1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.20 prefix-id 3
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.20 service slaac

set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.30
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.30 host-address ::1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.30 prefix-id 4
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface switch0.30 service slaac

commit
save
exit
```
## Quick note on restricting communication in VLAN30 and VLAN468
```
configure

set firewall group network-group VLAN_20 network 192.168.20.0/24
set firewall group network-group VLAN_30 network 192.168.30.0/24
set firewall group network-group LAN network 192.168.8.0/24
set firewall group network-group RFC_1918 network 192.168.0.0/16
set firewall group network-group RFC_1918 network 172.16.0.0/12
set firewall group network-group RFC_1918 network 10.0.0.0/18

set firewall name VLAN_30_IN default-action accept
set firewall name VLAN_30_IN rule 10 action accept
set firewall name VLAN_30_IN rule 10 destination group network-group VLAN_20
set firewall name VLAN_30_IN rule 10 state established enable
set firewall name VLAN_30_IN rule 10 state related enable
set firewall name VLAN_30_IN rule 10 protocol all
set firewall name VLAN_30_IN rule 20 action accept
set firewall name VLAN_30_IN rule 20 destination address 192.168.10.1
set firewall name VLAN_30_IN rule 20 destination port 137-139
set firewall name VLAN_30_IN rule 20 protocol tcp_udp
set firewall name VLAN_30_IN rule 30 action accept
set firewall name VLAN_30_IN rule 30 destination address 192.168.10.1
set firewall name VLAN_30_IN rule 30 destination port 445
set firewall name VLAN_30_IN rule 30 protocol tcp_udp
set firewall name VLAN_30_IN rule 40 action drop
set firewall name VLAN_30_IN rule 40 destination group network-group RFC_1918
set firewall name VLAN_30_IN rule 40 protocol all
set interfaces switch switch0 vif 30 firwall in name VLAN_30_IN
set firewall name VLAN_30_LOCAL default-action drop
set firewall name VLAN_30_LOCAL rule 10 action accept
set firewall name VLAN_30_LOCAL rule 10 destination address 192.168.30.1
set firewall name VLAN_30_LOCAL rule 10 destination port 53
set firewall name VLAN_30_LOCAL rule 10 protocol tcp_udp
set firewall name VLAN_30_LOCAL rule 20 action accept
set firewall name VLAN_30_LOCAL rule 20 destination port 67
set firewall name VLAN_30_LOCAL rule 10 protocol udp
set interfaces switch switch0 vif 30 firewall local name VLAN_30_LOCAL

set firewall name VLAN_468_IN default-action accept
set firewall name VLAN_468_IN rule 10 action accept
set firewall name VLAN_468_IN rule 10 destination group network-group VLAN_20
set firewall name VLAN_468_IN rule 10 state established enable
set firewall name VLAN_468_IN rule 10 state related enable
set firewall name VLAN_468_IN rule 10 protocol all
set firewall name VLAN_468_IN rule 20 action accept
set firewall name VLAN_468_IN rule 20 destination group network-group VLAN_30
set firewall name VLAN_468_IN rule 20 state established enable
set firewall name VLAN_468_IN rule 20 state related enable
set firewall name VLAN_468_IN rule 20 protocol all
set firewall name VLAN_468_IN rule 30 action accept
set firewall name VLAN_468_IN rule 30 destination address 192.168.10.1
set firewall name VLAN_468_IN rule 30 destination port 137-139
set firewall name VLAN_468_IN rule 30 protocol tcp_udp
set firewall name VLAN_468_IN rule 40 action accept
set firewall name VLAN_468_IN rule 40 destination address 192.168.10.1
set firewall name VLAN_468_IN rule 40 destination port 445
set firewall name VLAN_468_IN rule 40 protocol tcp_udp
set firewall name VLAN_468_IN rule 50 action drop
set firewall name VLAN_468_IN rule 50 destination group network-group RFC_1918
set firewall name VLAN_468_IN rule 50 protocol all
set interfaces switch switch0 vif 468 firewall in name VLAN_468_IN
set firewall name VLAN_468_LOCAL default-action drop
set firewall name VLAN_468_LOCAL rule 10 action accept
set firewall name VLAN_468_LOCAL rule 10 destination address 192.168.97.1
set firewall name VLAN_468_LOCAL rule 10 destination port 53
set firewall name VLAN_468_LOCAL rule 10 protocol tcp_udp
set firewall name VLAN_468_LOCAL rule 20 action accept
set firewall name VLAN_468_LOCAL rule 20 destination port 67
set firewall name VLAN_468_LOCAL rule 20 protocol udp
set interfaces switch switch0 vif 468 firewall local name VLAN_468_LOCAL

commit
save
exit
```
## Quick note on restricting Internet communication from VLAN_98 but can access VLAN_10 and VLAN_20
```
configure

set firewall group network-group VLAN_10 network 192.168.10.0/24
set firewall group network-group VLAN_20 network 192.168.20.0/24
set firewall group network-group VLAN_98 network 192.168.98.0/24

set firewall name VLAN_98_IN default-action drop
set firewall name VLAN_98_IN rule 10 action accept
set firewall name VLAN_98_IN rule 10 protocol all
set firewall name VLAN_98_IN rule 10 destination group network-group VLAN_10
set firewall name VLAN_98_IN rule 20 action accept
set firewall name VLAN_98_IN rule 20 protocol all
set firewall name VLAN_98_IN rule 20 destination group network-group VLAN_20
set firewall name VLAN_98_IN rule 30 action accept
set firewall name VLAN_98_IN rule 30 protocol all
set firewall name VLAN_98_IN rule 30 destination group network-group VLAN_98
set interfaces switch switch0 vif 98 firewall in name VLAN_98_IN

commit
save
exit
```
## Quick note on enabling mdns repeater
```
configure

set service mdns repeater interface switch0
set service mdns repeater interface switch0.20
set service mdns repeater interface switch0.30
set service mdns repeater interface switch0.468

commit
save
exit
```
