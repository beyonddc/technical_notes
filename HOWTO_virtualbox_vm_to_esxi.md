# Instruction on transitioning VMs from Virtual Box to ESXI
* Tested on
  * VirtualBox version 5.2.4
  * VMWare ESXI 6.5
  
1. Export VirtualBox VM to OVF format
2. Modify the *.ovf file in a text editor (e.g. notepad++)
  * In the ovf file change the System Type from `virtualbox-2.2` to `vmx-07`
    * `<vssd:VirtualSystemType>virtualbox-2.2</vssd:VirtualSystemType>`
    * `<vssd:VirtualSystemType>vmx-07</vssd:VirtualSystemType>`
  * Change the SATA controller to a SCSI controller.
from
```
<Item>
  <rasd:Address>0</rasd:Address>
  <rasd:Caption>sataController0</rasd:Caption>
  <rasd:Description>SATA Controller</rasd:Description>
  <rasd:ElementName>sataController0</rasd:ElementName>
  <rasd:InstanceID>5</rasd:InstanceID>
  <rasd:ResourceSubType>AHCI</rasd:ResourceSubType>
  <rasd:ResourceType>20</rasd:ResourceType>
</Item>
  ```
to
```
<Item>
  <rasd:Address>0</rasd:Address>
  <rasd:Caption>SCSIController</rasd:Caption>
  <rasd:Description>SCSI Controller</rasd:Description>
  <rasd:ElementName>SCSIController</rasd:ElementName>
  <rasd:InstanceID>5</rasd:InstanceID>
  <rasd:ResourceSubType>lsilogic</rasd:ResourceSubType>
  <rasd:ResourceType>6</rasd:ResourceType>
</Item>
```
  * Might need to remove this block altogether
```
<Item>
  <rasd:AddressOnParent>3</rasd:AddressOnParent>
  <rasd:AutomaticAllocation>false</rasd:AutomaticAllocation>
  <rasd:Caption>sound</rasd:Caption>
  <rasd:Description>Sound Card</rasd:Description>
  <rasd:ElementName>sound</rasd:ElementName>
  <rasd:InstanceID>6</rasd:InstanceID>
  <rasd:ResourceSubType>ensoniq1371</rasd:ResourceSubType>
  <rasd:ResourceType>35</rasd:ResourceType>
</Item>
```
  * Now you should be able to import the exported OVF into ESXI
# Unable to start imported VirtualBox CentOS/RHEL 7.x VM in ESXI (Referenced: https://wiki.centos.org/TipsAndTricks/CreateNewInitrd)
Error message shows the following during bootup
```
Starting Dracut Emergency Shell...
Warning: /dev/mapper/rhel-root does not exist
Warning: /dev/rhel/root does not exist
Warning: /dev/rhel/swap does not exist
```
This is likely due to the fact when switching out from SATA Controller to SCSI Controller when modifying the *.ovf file.  Follow below instructions to address the issue
* Set your RHEL 7.x VM to boot off from the RHEL 7.x ISO image (hint: you might need to set your VM to boot into BIOS to set the boot sequence to boot off from an ISO image or CD-ROM drive)
* In RHEL 7.x Boot Menu, select Troubleshooting -> Rescue a Red Hat Enterprise Linux System
* Once you hit the menu table, enter '1' to 'Continue'
* Type the following commands once you are in the shell (hint: use the latest initramfs image)
```
mount --bind /proc /mnt/sysimage/proc
mount --bind /dev /mnt/sysimage/dev
mount --bind /sys /mnt/sysimage/sys
mount --bind /run /mnt/sysimage/run
systemctl start multipathd.service
chroot /mnt/sysimage
dracut -f /boot/initramfs-3.10.0-693.21.1.el7.x86_64.img 3.10.0-693.21.1.el7.x86_64
exit
cd /
sync
telinit 6
```
* Your VM will restart at this point but it will attempt to boot into your RHEL 7.x install menu.  Power off the system and set your CD-ROM to host or remove the image then power on your system again
* Now wait... your system might auto reboot multiple times
