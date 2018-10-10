# Installing ESXi on a Dell Server for use as a Multimedia Server
We will be going over the process of installing the ESXi Hypervisor on a Dell  server and provisioning several virtual machines to run Plex Media Server along with other tools.

## Server Specs
For this project, I will be using a [Dell R320 Server](https://cdn.cnetcontent.com/2b/d0/2bd0225a-38b9-4b41-9a43-85ea38a463a5.pdf "Technical Guide"). The server came preconfigured with the following:
  * CPU- Xeon E5-2430
  * RAM- 2 x 8GB
  * RAID- PERC H710 with RAID 10 configuration
  * Storage- 4 x 500GB 2.5" SAS HDD
  * Power- Dual 350W PSUs

## Upgrades
#### Processor
I chose NOT to upgrade my processor at the moment as it was more than adequate for my needs. The best processor it supports is the Xeon E5-2470 with 8 cores/16 threads. Pages 15-16 of the Technical Guide provide a full list of compatible processors

#### RAM
I added an additional pair of 8GB RAM sticks to bring the RAM total to 32 GB. This server supported up to 192 GB of RAM across 6 slots. 32 GB is plenty for my use but if you require more memory, be sure to check page 17 of the Technical Guide to determine the appropriate RAM to purchase.
For this project I used the following RAM: (INSERT LINK HERE)

#### Storage
I replaced all the drives in this server with seven 4 TB 2.5" SATA drives and one 250GB SSD in a USB enclouser. This will provide 28 TB of raw storage, however, for redundancy we will be using RAID to have drive failovers in case drivers file. Using RAID 5 will provide 24TB of raw storage and RAID 6 provides 20TB of raw storage. For my case, I will be using RAID 5 to have the maximum amount of storage available for my media. This server also has one internal USB A 2.0 connector, which I will use to connect an 8GB flash drive to serve as the ESXi boot device. The 250GB SSD will be used to house the Windows 10 virtual machines for Plex and Plex Media Tools and will be in a RAID 0 configuration.

#### PCI Expansion
This server has 1 x PCIe x8 and 1 x PCIe x16 slots for expansion. I plan to eventually add a quad NIC.

## Final Server Specs
These are the final specs of my rebuilt server
  * CPU- Xeon E5-2430
  * RAM- 4 x 8GB
  * RAID- PERC H710 with RAID 5 configuration
  * Storage- 1 x 250 GB SATA SSD (RAID 1) 7 x 4TB 2.5" SATA HDD (RAID 5)
  * Power- Dual 350W PSUs
