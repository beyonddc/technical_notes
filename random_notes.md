# SSH forwarding doesn't work
* Check /etc/ssh/sshd_config and ensure it has the following line 'AddressFamily inet'

# Install nodejs on Ubuntu 16.x
* curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
* sudo apt-get install -y nodejs

# Reverting to legacy Network Naming Scheme
* Edit **/etc/default/grub** and add **net.ifnames=0 biosdevname=0** to line **GRUB_CMDLINE_LINUX**
..* For example **GRUB_CMDLINE_LINUX=" crashkernel=auto net.ifnames=0 biosdevname=0 rhgb quiet"**
* Regenerate GRUB configuration file and overwrite existing one:
..* # grub2-mkconfig -o /boot/grub2/grub.cfg
* Create **/etc/sysconfig/network-scripts/ifcg-eth0**
* Disable NetworkManager by **systemctl disable NetworkManager**
* Reboot system by **shutdown -r now**

# Enable nested virtualization on ESXI Server
* Modify the VM's.vmx file and add "vhv.enable = TRUE"

# VMWare ESXI 6.7 - Clone VM and perserve thin pervision
* Enable SSH on your ESXI server
* Use vmkfstools for cloning (e.g. **vmkfstools -i "JMPS RHEL 7.x - 64Bit.vmdk" /vmfs/volumes/DataStore2_3TB/VMs/JMPS_RHEL_7.x-64Bit_guacamole_server/JMPS_RHEL_7.x-64Bit.vmdk -d thin**)

# Unable to boot a VM in VMWare
Seems like changing `firmware = "efi"` to `firmware = "bios"` in the *.vmx file fixed the issue
