# Exporting from VMWare Player or Virtual Box?
Exported virtual appliance in OVF format 1.0 using Virtual Box 5.1.26 and importing it to VMware Workstation 14 Player will display a warning stating "*.ova did not pass OVF specification conformance or virtual hardware compliance checks".
Exported virtual appliance using the ovftool.exe from VMWare Workstation 14 Player and importing it to Virtual Box 5.1.26 would work perfectly fine.

# Network adapter name changed when importing virtual appliance (CentOS 7.x or RHEL 7.x)
This is caused by biosdevname renaming the device.  To address the issue, do the following
* vi /etc/default/grub
* Add the following to GRUB_CMDLINE_LINUX
..* biosdevname=0
..* net.ifnames=0
* To make this change active, run the following command
..* grub2-mkconfig -o /boot/grub2/grub.cfg
* Reboot 
