# Installing ESXi on a Dell Server for use as a Multimedia Server
We will be going over the process of installing the ESXi Hypervisor on a Dell  server and provisioning several virtual machines to run Plex Media Server along with other tools.

#### Technical Guide
https://cdn.cnetcontent.com/2b/d0/2bd0225a-38b9-4b41-9a43-85ea38a463a5.pdf

## Server Specs
For this project, I will be using a Dell R320 Server. The server came preconfigured with the following:
  * CPU- Xeon E5-2430
  * RAM- 2 x 8GB
  * RAID- PERC H710 with RAID 10 configuration
  * Storage- 4 x 500GB 2.5" SAS HDD
  * Power- Dual 350W PSUs
  * 1 x PCIex8
  * 2 x PCIex16

## Upgrades
#### Processor
I chose NOT to upgrade my processor at the moment as for my needs, it was more than adequate. The best processor it supports is the Xeon E5-2470 with 8 cores/16 threads. Pages 15-16 provide a full list of compatible processors

#### RAM
I added an additional pair of 8GB RAM sticks to bring the RAM total to 32 GB. This server supported up to 192 GB of RAM across 6 slots. 32 GB is plenty for my use but if you require more memory, be sure to check page 17 of the technical guide to determine the appropriate RAM to purchase.
For this project I used the following RAM: (INSERT LINK HERE)

#### Storage
I replaced all the drives in this server with seven 4 TB 2.5" SATA drives and one 120GB SSD. This will provide a 28 TB of raw storage, however, for redundancy we will be using RAID to have drive failovers in case drivers file. Using RAID 5 will provide 24TB of raw storage and RAID 6 provides 20TB of raw storage. For my case, I will be using RAID 5 to have the maximum amount of storage available for my media.

#### PCI Expansion
I had a spare quad-port NIC lying around at my office that I decided to throw into the PCIex8 port to dedicate a NIC per VM. This is NOT necessary as you can share the two NICs that are built into the server.

## Installing ESXi

## Plex VM

## Plex Tools VM
#### Tautulli
https://tautulli.com
#### CouchPotato
https://couchpota.to/
#### Sonarr
https://sonarr.tv/
#### Ombi
https://ombi.io/
#### Deluge
https://dev.deluge-torrent.org/wiki/Download

## Pi-Hole
https://pi-hole.net/
