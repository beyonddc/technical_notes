# Enable nested virtualization on ESXI Server
* Modify the VM's.vmx file and add "vhv.enable = TRUE"

# VMWare ESXI 6.7 - Clone VM and perserve thin pervision
* Enable SSH on your ESXI server
* Use vmkfstools for cloning (e.g. **vmkfstools -i "JMPS RHEL 7.x - 64Bit.vmdk" /vmfs/volumes/DataStore2_3TB/VMs/JMPS_RHEL_7.x-64Bit_guacamole_server/JMPS_RHEL_7.x-64Bit.vmdk -d thin**)

# Unable to boot a VM in VMWare
Seems like changing `firmware = "efi"` to `firmware = "bios"` in the *.vmx file fixed the issue

# Export VM to OVF from ESXI Server using OVF Tool
In this example, the OVF Tool is installed on a Windows host.  
`PS C:\Program Files\VMware\VMware OVF Tool> .\ovftool.exe vi://root@<ip_address>/<vm_name> "C:\<windows_path>\<vm_name>.ovf"`  
Example:  
`PS C:\Program Files\VMware\VMware OVF Tool> .\ovftool.exe vi://root@192.168.8.99/Foo "C:\Users\beyonddc\Desktop\Foo\Foo.ovf"`

# Register a VM using vim-cmd on an ESXI Server
`vim-cmd solo/registervm /vmfs/volumes/5ad63960-9e683519-7280-000e0cc162f3/VMs/Foo/Foo.vmx`

# Some rough steps on extending disk space for a LVM on RHEL/CentOS on an ESXI Server
* Create a new hard disk for the VM on the ESXI Server
* Start up the VM
* Log into the VM and open a termainl as root
* Use `fdisk` to create partition for the new added hard disk (e.g `fdisk /dev/sdb`), 1) add new partitation and 2) write table to dsk and exit
* Initialize the partition using `pvcreate` command (e.g. `pvcreate /dev/sdb1`)
* Extend the volume group (e.g. `vgextend <volume_group_name> /dev/sdb1`).  Use `vgdisplay` to display volume group information, and `pvscan` to verify volume group after it is extended
* Extend the logical volume (e.g. `lvextend <logical_volume_name> /dev/sdb1`)  Use `lvdisplay` to display logical volume information
* Grow the logical volume (e.g. `xfs_growfs <logical_volume_name>`)
* Verify disk space is growed successfully (e.g. `df -h`)

# To display memory information of each slot via CLI on the ESXI node
`smbiosDump |grep -A 12 'Memory Device'`

# To display memory summary via CLI on the ESXI node
`smbiosDump |grep -A 4 'Physical Memory Array'`
