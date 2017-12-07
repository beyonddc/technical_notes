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

## Quick note on restricting communication between VLAN 20 and VLAN 30
configure

set firewall group address-group 20_30_ROUTER_IP address 192.168.20.1
set firewall group address-group 20_30_ROUTER_IP address 192.168.30.1

set firewall group network-group 20_30_VLAN_NETS network 192.168.20.0/24
set firewall group network-group 20_30_VLAN_NETS network 192.168.30.0/24

set firewall name 20_30_VLAN_IN default-action accept

set firewall name 20_30_VLAN_IN rule 10 action accept
set firewall name 20_30_VLAN_IN rule 10 destination group address-group 20_30_ROUTER_IP

set firewall name 20_30_VLAN_IN rule 20 action drop
set firewall name 20_30_VLAN_IN rule 20 destination group network-group 20_30_VLAN_NETS

set interfaces switch switch0 vif 20 firwall in name 20_30_VLAN_IN
set interfaces switch switch0 vif 30 firwall in name 20_30_VLAN_IN

commit
save
exit
