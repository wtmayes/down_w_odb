# down_w_odb
# Ansible automation to standardize OOB/DMI/BIOS on SMC nodes

Check nodes process
Assumptions:
  -	Ansible on a master laptop
  -	Nodes are configured for DHCP
  -	DHCP server running in the IP space (vlan 53)
  -	SUM tarball is extracted under the files directory
  -	User is root
  
The files required to run are:
  -	The seven .yml files, called in order from role update_oob_dmi_bios.yml,
  -	The most current BIOS.ROM file from SMC or SealingTech
  -	The consolidated list file—all the node data
  -	Two BIOS config .xml files for baseline, then boot order.
  -	The “SUM” executable (as a tar.gz from SuperMicro) to do nearly all this work out-of-band (OOB) 
  
The playbook(s) consists of 
  -	Check nodes master
  -	And 6 loops that run to
  -	Take stock of connected nodes and gathers their values (SN, Asset Tag, IPMI MAC, OOB key, and Baseboard ID) 
  -	Check whether the OOB license has been applied, 
  - Check whether the BIOS requires an upgrade, 
  -	Check if the SealingTech serial number, Asset tag, and FGGA Army brand has been applied
  -	Check to see if all the drives and memory for each node is seen by BIOS
  -	Then incrementally standardizes the BIOS configs thru 3 reboots—
    o	1)sets the node to defaults, 
    o	2)enables PXE, SR-IOV, PCIe and custom CPU settings, 
    o	3) sets boot order
    
 The role is agnostic as to 
  -	whether the nodes are all from the same kit
  -	how many nodes are connected (1..x)
  -	Whether the nodes are Accessory or Spare nodes.
  -	Whether there is an OS on the target nodes
  -	No need to make ssh connections to servers
  
   The work is all done thru an IPMI and it’s APIs, via a powerful set of shell commands from SMC.  See the SUM user guide in the tarball.
   The consolidated list file has 815 nodes listed (more to come) and only 5 empty data cells.  Kits 22, 23, 48, 55, and 82 are missing one node’s data each--no idea what happens if we try to run the playbook on those nodes missing data

   I’ll send out an updated consolidated list textfile as soon as I can corral the missing info.  Due-out now to SealingTech for a list of mobos in kits 90-109.

   Thanks to Matt Cundari, Dave Hoskins and Will Stutt for letting me bug them with stupid questions.  The json query from Matt made a huge difference.
