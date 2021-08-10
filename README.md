# down_w_odb
# Ansible automation to standardize OOB/DMI/BIOS on SMC nodes

Check nodes process
Assumptions:
  -	Ansible on a master laptop
  -	Nodes are configured for DHCP
  -	DHCP server running in the IP space (vlan 53)
    (OR using a static list textfile--see comments in main.yml)
  -	SUM tarball is extracted under the files directory
  -	User is root and folders/subfolders and all files from this repo are "chmod -R 755 *"
  
The files required to run are:
  -	The seven .yml files, called in order from role update_oob_dmi_bios.yml,
  -	The most current BIOS.ROM file from SMC or SealingTech
  -	The consolidated list file—all the node data
  -	Two BIOS config .xml files for baseline, then boot order.
  -	The “SUM” executable (as a tar.gz from SuperMicro) to do nearly all this work out-of-band (OOB) 
  -	(The bios .ROM had to be zipped to meet file size requirements on GH--unzip and move to files dir)
  
The playbook(s) consists of 
  -	Check nodes master
  -	And 6 loops that run to
  -	Take stock of connected nodes and gathers their values (SN, Asset Tag, IPMI MAC, OOB key, and Baseboard ID) 
  -	Check whether the OOB license has been applied, 
  - Check whether the BIOS requires an upgrade, 
  -	Check if the SealingTech serial number, Asset tag, and FGGA Army brand has been applied
  -	Check to see if all the drives and memory for each node is seen by BIOS
  -	Then incrementally standardizes the BIOS configs thru 3 reboots
 
    o	1) sets the node to defaults,
    o	2) enables PXE, SR-IOV, PCIe and custom CPU settings,
    o	3) sets boot order
    
 The role is agnostic as to 
  -	whether the nodes are all from the same kit
  -	how many nodes are connected (1..x)
  -	Whether the nodes are Accessory or Spare nodes.
  -	Whether there is an OS on the target nodes
  -	A need to make ssh connections to servers
  
   The work is all done thru an IPMI and its APIs, via a powerful set of shell commands from SMC.  See the SUM user guide in the tarball.
   The consolidated list file has 975 nodes listed NOW for all kits from 1-109 -- and no empty data cells.  90 Accessory nodes and 13 Spare nodes are also on the list.

   We will have to make some future updates to consolidated_list.txt as nodes are repaired with new mainboards.  BIOS/firmware revisions may also require future updates.
   Thanks to Matt Cundari, Dave Hoskins and Will Stutt for letting me bug them with stupid questions.  The json query from Matt made a huge difference.
