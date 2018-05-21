# Enable nested virtualization on ESXI Server
* Modify the VM's.vmx file and add "vhv.enable = TRUE"

# VMWare ESXI 6.7 - Clone VM and perserve thin pervision
* Enable SSH on your ESXI server
* Use vmkfstools for cloning (e.g. **vmkfstools -i "JMPS RHEL 7.x - 64Bit.vmdk" /vmfs/volumes/DataStore2_3TB/VMs/JMPS_RHEL_7.x-64Bit_guacamole_server/JMPS_RHEL_7.x-64Bit.vmdk -d thin**)

# Unable to boot a VM in VMWare
Seems like changing `firmware = "efi"` to `firmware = "bios"` in the *.vmx file fixed the issue
