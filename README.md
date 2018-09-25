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
I replaced all the drives in this server with seven 4 TB 2.5" SATA drives and one 120GB SSD. This will provide 28 TB of raw storage, however, for redundancy we will be using RAID to have drive failovers in case drivers file. Using RAID 5 will provide 24TB of raw storage and RAID 6 provides 20TB of raw storage. For my case, I will be using RAID 5 to have the maximum amount of storage available for my media.

#### PCI Expansion
This server has 1 x PCIe x8 and 1 x PCIe x16 slots for expansion. I plan to eventually add a quad NIC.

## Final Server Specs
These are the final specs of my rebuilt server
  * CPU- Xeon E5-2430
  * RAM- 4 x 8GB
  * RAID- PERC H710 with RAID 5 configuration
  * Storage- 7 x 4TB 2.5" SATA HDD & 1 x 120 GB SATA SSD
  * Power- Dual 350W PSUs

## Installing Hypervisor
#### Preparing Server

#### Preparing Installing Media

#### Preparing Boot Media
 
#### Installing ESXi

#### Managing ESXi
In order to start managing and creating VMs, we will first need to connect to our ESXi host. ESXi 6.5 and above do not use the vSphere client, you can manage it directly through a browser.

## Creating VMs
#### Creating Virtual Machines
To run my Plex Server, I've decided to go with Windows 10 Pro. Many people will argue for/against this and other Operating Systems. I picked Windows 10 Pro due to my familiarity with it and you can delay when updates are installed. You can choose any OS you want, but for this guide we will be focusing on Windows 10. I have also made static IP assignments on my router for my Plex Server (192.168.5.10) and Plex Tools (192.168.5.11). 

## Plex VM
Since this VM will be on 24/7, I've decided to create two user accounts, Administrator and PMS, with PMS being a standard user. I prefer running Plex as a Windows service so that it will auto start when I reboot the VM for updates or maintance. We will be using NSSM, the Non-Sucking Service Manager, which you can download [here](https://nssm.cc/download). Open the ZIP file and copy the contents of the "win64" folder to "C:\Windows\System32". This allows use to run NSSM via the command line.

We will now be installing the latest version Plex, which you can always [download here](https://www.plex.tv/media-server-downloads/). If you have an SSD, I would recommend installing Plex on it, as it can provide some improvements with transcoding and loading metadata. You can read more about that [here](https://support.plex.tv/articles/200889878-matching-process/#toc-2). Follow all the prompts to install the Plex Media Server, but do not launch it. Plex requires port 32400 in order to function as a server. We will be doing this on the Windows Firewall AND the router.

Open a command prompt and type "nssm install Plex"

## Plex Tools VM
Since this VM will be on 24/7, I've decided to create two user accounts, Administrator and PMST, with PMST being a standard user. Every Plex tool requires a [port](https://www.reddit.com/r/explainlikeimfive/comments/1t9s5a/eli5_what_are_ports_ex_tcp_port/ce5tbfs) to access your network and internet. Port numbers range from 1-65,535 with ports 1-1024 reserved for the system. You can choose any ports after 1024 for your services. For this project, I chose 3808X, with X starting at 0 and going up for each additional service.

#### Caddy (Reverse Proxy/SSL)
We will first start by downloading our reverse proxy tool [here](https://caddyserver.com/download). Create a folder called "caddy" at the root of the C drive, extract the contents of the zip folder to that directory. Next, we will forward ports 80 and 443 on the Windows Firewall AND the router. Copy the contents of the following [Caddy File](https://github.com/officialJCReyes/dell-esxi-plex/blob/master/caddy-file) and place it in the Caddy folder you created. I recommend using [Notepad ++](https://notepad-plus-plus.org/download/v7.5.8.html) to edit the file. When saving the file, DO NOT add an extenstion to it.

Inside the Caddy folder, create a subfolder called "php". Download the following [PHP 7.0](https://windows.php.net/downloads/releases/php-7.0.32-nts-Win32-VC14-x64.zip) file and extract the contents to C:\caddy\php

To run Caddy as a service, open a command prompt and type "nssm install Caddy"

#### Tautulli (Plex Monitoring, Reporting and Newsletters)
[Download](https://github.com/Tautulli/Tautulli/releases)
Port 38080
Open a command prompt and type "nssm install Tautulli"

#### VPN
We will be using Private Internet Access as our VPN provider and OpenVPN for the connection. Download and install[OpenVPN](https://openvpn.net/index.php/open-source/downloads.html) with all defaults and the [PIA OVPN Files](https://www.privateinternetaccess.com/openvpn/openvpn.zip). Extract the contents of the zip file to "C:\Users\USERNAME\OpenVPN\config".

#### Deluge (light-weight torrent client)
We will need to download the latest version of Deluge from [here](https://dev.deluge-torrent.org/wiki/Download). Go ahead and also download the[WebAPI](https://github.com/idlesign/deluge-webapi/blob/master/dist/WebAPI-0.2.1-py2.7.egg). Install Deluge with all defaults and open it once installed. Go to Preferences -> Plugins and enable Extractor, Label and WebUI. Under WebUI, enable the web interface and change the default port number to 38081.

Open a command prompt and type "nssm install Deluged". Use the following configuration: . Back at the command prompt, type "nssm install Deluge-Web" and use the following configuration. Open your favorite browser and go to 127.0.0.1:38081. Enter deluge as the default password. You will be prompted to change the password which is recommended.

To install the EGG file we downloaded earlier, go to Preferences-> PLugins -> Install. Browse to the directory where you downloaded the file, select it and press install.

#### CouchPotato (Organizes and obtains movies)
[Download](https://couchpota.to/)
Port 38082
Open a command prompt and type "nssm install CouchPotato"

#### Sonarr (Organizes and obtains TV shows)
[Download](https://sonarr.tv/)
Port 38083
Open a command prompt and type "nssm install Sonarr"

#### Ombi (Allows users to request TV and Movies)
[Download](https://ombi.io/)
Port 38084
Open a command prompt and type "nssm install Ombi"

#### Jackett (Aggregator)
[Download](https://github.com/Jackett/Jackett)
Port 38085
Open a command prompt and type "nssm install Jackett"

## Pi-Hole
https://pi-hole.net/

## Final Remarks

Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")
