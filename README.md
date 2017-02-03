# Convert Virtualbox to VMware
___
Migrating to another virtual machine program can be intimidating. if you already have your virtual machines set up they way you like them, you don’t necessarily have to install them from scratch – you can migrate your existing virtual machines. This small howto guide will help you further in successfully converting your Virtualbox machine to VMware. 

## Tools
______
1. OVT Tool (you will need an account on VM-ware: [More info & download]( http://communities.vmware.com/community/vmtn/server/vsphere/automationtools/ovf)
2.  Microsoft Checksum Verify utility: [More info & download](https://support.microsoft.com/nl-nl/help/841290/availability-and-description-of-the-file-checksum-integrity-verifier-utility)
3.  Some Text Editor

## Howto
#### Step one
______
Before you start, you have to make sure your virtual machine's power is off.
![alt text](http://www.howtogeek.com/wp-content/uploads/2012/10/650x295ximage1.png.pagespeed.gp+jp+jw+pj+js+rj+rp+rw+ri+cp+md.ic.dU9S65UNfd.png)

Next, go to file and select Export Appliance.
![alt text](http://www.howtogeek.com/wp-content/uploads/2012/10/650x218ximage2.png.pagespeed.gp+jp+jw+pj+js+rj+rp+rw+ri+cp+md.ic.rqxcgrMyO4.png)

Select the virtual machine you want to export and select the location you want to store the .ova file.

#### Step two
______
If you try to import the .ova file in the VMware, you will get some errors. You first have to convert this to an .ovf file. Make sure you have installed OVT tool for this step. Run the following command in your Command line:
```
ovftool.exe –lax <source.ova>  <destination.ovf> 
```

#### Step three
______
Now you still have to make a few changes in your .ovf file. Open your freshly made file with some text editor and find following line: 

```
<vssd:VirtualSystemType>virtualbox-2.2</vssd:VirtualSystemType>
```
and change it to:
```
<vssd:VirtualSystemType>vmx-07</vssd:VirtualSystemType>  
```
Next search for the following in the same file :
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
Change this part with:
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
Exit and save the file
#### Step four
______
We still have to change one thing but this time you have to open the .mv file in a text editor. You will find this file in the same location as your .ovf file. 
You will need Microsoft Checksum Verify utility for this step. I have saved this tool in the temp folder. Go to this folder and copy the following command:
```
fciv.exe -sha1 <filename.ovf>
```
![alt text](http://www.itsecurenet.com/wp-content/uploads/2013/01/OVF-file-new-SHA1.png)
In the picture you see the sha1 line (yellow part). Copy this and change the existing sha1 code in the .mv file. Save and exit the file.

#### Step five
______
Open the vSphere client and go to file -> Deploy OVF template...
Choose the .ovf template you have made and click on Next. 
Set the configurations the way you want and you are ready to use your Virtual Machine.

## Sources
______
* http://www.howtogeek.com/125640/how-to-convert-virtual-machines-between-virtualbox-and-vmware/
* http://www.itsecurenet.com/virtualbox-ova-to-vsphere-ovf/
