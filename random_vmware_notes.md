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
