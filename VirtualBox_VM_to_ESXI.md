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
