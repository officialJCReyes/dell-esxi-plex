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

## Installing Hypervisor
#### Preparing Server
We will start by turning on our server, selecting F10 to get into the Lifecycle Controller. Select the FTP option. We will use the following server to obtain the latest updates from Dell for our specific server: ftp.dell.com.

#### Preparing Installing Media

#### Preparing Boot Media
First we will go to Dell's Support [site](suport.dell.com) to download the latest ESXi for our server. Going through Dell's site allows you to download a custom ESXi image that has the drivers builtin in for your specific server.

##### Enter your Dell Service Tag
![alt text](https://i.imgur.com/tbSfaie.jpg "Dell Support Site")  
##### Select Drivers & Downloads -> Change Operating System to "VMware ESXi 6.5" -> Change Category to "Enterprise Solutions" -> Click "Download" for VMware ESXi 6.5 U2
![alt text](https://imgur.com/vogJvaB.jpg "Dell ESXi Download")  

Now that we have our ISO, we wil need to create our bootable media. The ISO is only 350 MB so a 1 GB flash drive will suffice. To make the USB drive bootable, we'll use a popular tool called Rufus. [Download](http://rufus.akeo.ie/) the tool and run it once it is installed.

##### Your USB drive will automatically be populated. Click "Select" to browser to the ISO you downloaded. Leave all other settings and click "start
![alt text](https://imgur.com/mz69kXX.jpg "Rufus ESXi USB")   
 
#### Preparing Server
We now need to configure the RAID controller to recognize our drives and put them in our single drive configuration (1 SSD) and RAID 5 configuration (seven 4TB drives) for ESXi to be able to recognize and access.

Let's boot up our server and select F10 on boot to get to the Lifecycle Controller. When you are at the Lifecycle Controller interface, select "Configure RAID", since we only have one controller, select "Next". On the following screen, we can pick our RAID configuration, we will use RAID 5. On the the third step, we will select all of the drives for our RAID 5 and press "Next". TO create our virtual disk, we need to give it a name. I will call mine "Media". On the Summary Page, click on Finish and click Yes on the acknowledge page regarding wiping the drives. When it is done, go ahead and exit so you can reboot.

#### Installing ESXi
Connect your installation media to any free USB port and boot up your server. Press F11 when you are at the loading screen and when you are given the option, select UEFI Boot. Select your install media, which in my case was labeled "front USB"

#### Managing ESXi
In order to start managing and creating VMs, we will first need to connect to our ESXi host. ESXi 6.5 and above does not use the vSphere client, you can manage it directly through a browser. You can find the IP address of your ESXi host on the server. On the monitor you will seee the screen split in grey and yellow. The IP address will be listed in the lower half of the screen. In our case it is 10.0.1.19. (I was configuring this device at work so my IP address structure will be different than my final location)

![alt text](https://imgur.com/R8uMrOI.jpg "ESXi Login Screen")  

We will use *root* and the password we created after we installed ESXi. You will now be presented with the host page for ESXi. Here you will find all the basic information of your server such as RAM, CPU, what image you are using etc.

![alt text](https://imgur.com/9zrzjpQ.jpg "ESXi Main Screen")  

The next few screenshots will walk you through how to create a datastore so that you have a location for your virtual machines to live.

![alt text](https://imgur.com/cO7EQ6c.jpg "ESXi New Datastore 01")  
![alt text](https://imgur.com/cwRWCwV.jpg "ESXi New Datastore 02")  
![alt text](https://imgur.com/C8Z0tyR.jpg "ESXi New Datastore 03")  
![alt text](https://imgur.com/I3pIPsu.jpg "ESXi New Datastore 04")  
![alt text](https://imgur.com/4PplKrm.jpg "ESXi New Datastore 05")  
![alt text](https://imgur.com/929dNyL.jpg "ESXi New Datastore 06")  

With our datastore created, we will now need to upload ISOs for the operating system(s) we want to install on our virtual machines. Since I will be using Windows 10, we will create a folder to house our ISOs and then upload the Windows 10 ISO.

![alt text](https://imgur.com/SJpTf8y.jpg "ESXi Adding ISOs 01")  
![alt text](https://imgur.com/jRIbuqa.jpg "ESXi Adding ISOs 02")
![alt text](https://imgur.com/JXQhXAV.jpg "ESXi Adding ISOs 03")  
![alt text](https://imgur.com/udOADMV.jpg "ESXi Adding ISOs 04")
![alt text](https://imgur.com/4S3m6Pv.jpg "ESXi Adding ISOs 05")

## Creating VMs
#### Creating Virtual Machines
To run my Plex Server, I've decided to go with Windows 10 Pro. Many people will argue for/against this and other Operating Systems. I picked Windows 10 Pro due to my familiarity with it and you can delay when updates are installed. You can choose any OS you want, but for this guide we will be focusing on Windows 10. I have also made static IP assignments on my router for my Plex Server (192.168.5.10) and Plex Tools (192.168.5.11). 

![alt text](https://imgur.com/7snbPkB.jpg "ESXi Creating a VM 01")  
![alt text](https://imgur.com/tGgeH47.jpg "ESXi Creating a VM 02")
![alt text](https://imgur.com/lrp2cFw.jpg "ESXi Creating a VM 03")  
![alt text](https://imgur.com/rtXgfVV.jpg "ESXi Creating a VM 04")
![alt text](https://imgur.com/bg3HSIa.jpg "ESXi Creating a VM 05")
![alt text](https://imgur.com/18ervsj.jpg "ESXi Creating a VM 06")
![alt text](https://imgur.com/RCEL4R3.jpg "ESXi Creating a VM 07")
![alt text](https://imgur.com/ir0nBfS.jpg "ESXi Creating a VM 08")

Now that we have created a virtual machine and gave a bootable device, we will want to turn it on and access a virtual monitor to start our operating system install.

![alt text](https://imgur.com/b6XV0Jl.jpg "ESXi Turning on a VM")  
![alt text](https://imgur.com/zmUzr7H.jpg "ESXi Opening VM Console")

With the console open, you can now proceed with the standard installation of the operating system of your choosing 

## Plex VM
Since this VM will be on 24/7, I've decided to create two user accounts, Admin and PMS, with PMS being a standard user and which will be logged in all the time. I prefer running Plex as a Windows service so that it will auto start when I reboot the VM for updates or maintance. We will be using NSSM, the Non-Sucking Service Manager, which you can download [here](https://nssm.cc/download). Open the ZIP file and copy the contents of the *win64* folder to *C:\Windows\System32*. This allows use to run NSSM via the command line.

We will now be installing the latest version Plex, which you can always [download here](https://www.plex.tv/media-server-downloads/). If you have an SSD, I would recommend installing Plex on it, as it can provide some improvements with transcoding and loading metadata. You can read more about that [here](https://support.plex.tv/articles/200889878-matching-process/#toc-2). Follow all the prompts to install the Plex Media Server, but do not launch it. Plex requires port 32400 in order to function as a server. We will be doing this on the Windows Firewall AND the router.

Open a command prompt and type *nssm install Plex*

**Path:** 'C:\Program Files (x86)\Plex\Plex Media Server\Plex Media Server.exe'  
**Startup Directory:** 'C:\Program Files (x86)\Plex\Plex Media Server' 

#### Caddy (Reverse Proxy/SSL)
We will now install our reverse proxy tool which you can download here [here](https://caddyserver.com/download). Create a folder called "caddy" at the root of the C drive, extract the contents of the zip folder to that directory. Next, we will forward ports 80 and 443 on the Windows Firewall AND the router. Copy the contents of the following [Caddy File](https://github.com/officialJCReyes/dell-esxi-plex/blob/master/caddy-file) and place it in the Caddy folder you created. I recommend using [Notepad ++](https://notepad-plus-plus.org/download/v7.5.8.html) to edit the file. When saving the file, DO NOT add an extenstion to it.

Inside the Caddy folder, create a subfolder called *php*. Download the [PHP 7.0](https://windows.php.net/downloads/releases/php-7.0.32-nts-Win32-VC14-x64.zip) zip file and extract the contents to *C:\caddy\php*. Now to run Caddy as a service upon start up, open a command prompt and type *nssm install Caddy*

**Path:** 'C:\caddy\caddy.exe'  
**Startup Directory:** 'C:\caddy'  

Save the NSSM settings and when you're back the command prompt, type *nssm start Caddy*. Open a new browser tab and head to 127.0.0.1:2015.

## Plex Tools VM
Since this VM will be on 24/7, I've decided to create two user accounts, Admin and PMST, with PMST being a standard user and which will be logged in all the time. Every Plex tool requires a [port](https://www.reddit.com/r/explainlikeimfive/comments/1t9s5a/eli5_what_are_ports_ex_tcp_port/ce5tbfs) to access your network and internet. Port numbers range from 1-65,535 with ports 1-1024 reserved for the system. You can choose any ports after 1024 for your services. For this project, I chose 3808**X**, with X starting at 0 and going up for each additional service. If you decide to use different ports, be sure to make a note of them and adjust the rest of the guide accordingly. The majority of our tools will be stored in *C:\Tools*. Let us create a firewall rule to allow our programs network access via the port they will be running on. Open Control Panel -> Windows Defender Firewall -> Advanced Settings. Right click *Inbound Rules* and click on *New Rule*. Select Port, leave it on TCP and enter your port ranges, in our case 38080-38085 since we are running 6 services. Allow the connection, apply the rule on all networks, give it a name and finish.

#### Tautulli (Plex Monitoring, Reporting and Newsletters)
Let's being by downloading the latest release of [Python 2.7](https://www.python.org/downloads/release/python-2715/) and the Tautulli [zip file](https://github.com/Tautulli/Tautulli/releases). Run the Python installer leaving all defaults and extract the contents of the zip file to *C:\Tools\Tautulli*. Open a command prompt and type *nssm install Tautulli*. Use the following settings for NSSM: 

**Path:** 'C:\Python27\pythonw.exe'  
**Start directory:** 'C:\Python27'  
**Arguments:** 'C:\Tools\Tautulli\Tautulli.py'  

![alt text](https://imgur.com/2u0VTsg.jpg "Tautulli NSSM") 

Save the NSSM settings and when you're back the command prompt, type *nssm start CouchPotato*. Open a new browser tab and head to [127.0.0.1:8181](http://127.0.0.1:8181). You should now see the setup wizard for Tautulli. Proceed to fill in the details for your Plex Server environment. 

With Tautulli now connected to your Plex Server, let's head over to Settings -> Web Interface and change the *HTTP Port* to 38080. Under *Public Tautulli Domain* I will be using https://domain.com/stats and stats as the *HTTP Root*. Finally, check the box for *Enable HTTP Proxy* so that we can properly proxy through Caddy. If you aren't prompted, restart Tautulli and you should now be able to connect to it via [127.0.0.1:38080/stats](http://127.0.0.1:38080/stats).

#### Deluge (light-weight torrent client)
We will need to download the latest version of Deluge from [here](https://dev.deluge-torrent.org/wiki/Download). Go ahead and also download the [WebAPI](https://github.com/idlesign/deluge-webapi/blob/master/dist/WebAPI-0.2.1-py2.7.egg). Install Deluge with all defaults and open it once installed. Go to Preferences -> Plugins and enable Extractor, Label and WebUI. Under WebUI, enable the web interface and change the default port number to 38081. Open command prompt and type *nssm install Deluged*. Use the following settings for NSSM: 

**Path:** 'C:\Program Files\Deluge\deluged-debug.exe'  
**Start directory:** 'C:\Program Files\Deluge'  
**Arguments:** '-c C:\config_location'  
![alt text](https://imgur.com/ruuY3WH.jpg "Deluged NSSM")  
Back at the command prompt, type *nssm install Delugew*. Use the following settings for NSSM: 

**Path:** 'C:\Program Files\Deluge\deluge-web-debug.exe'  
**Start directory:** 'C:\Program Files\Deluge'  
**Arguments:** '-c C:\config_location'  
![alt text](https://imgur.com/oBTp4NB.jpg "Delugew NSSM")  
Back at the command prompt, be sure both services are running by typing *nssm start Deluged* and *nssm start Delugew*.

Open your favorite browser and go to [127.0.0.1:38081](http://127.0.0.1:38081). Enter *deluge* as the default password. You will be prompted to change the password which is recommended. To install the EGG file we downloaded earlier, go to Preferences-> Plugins -> Install. Browse to the directory where you downloaded the file, select it and press install. We will make two final changes to tell Deluge where to store download files as well as where to locate torrent files. Go to Preferences-> Downloads and under *Download to* change the directory to *C:\Users\<USERNAME>\Downloads\Torrents*. Check the box for *Autoadd .torrent files from* and change the directory to *C:\Users\<USERNAME>\Downloads\Torrent Files*.

In order to maximize your download speeds, you should follow this [guide](https://www.rapidseedbox.com/kb/ultimate-deluge-guide#speedx) from RapidSpeedBox. Making those adjustments are out of the scope of this guide as these settings will vary greatly depending on your ISP, network speeds among other things.

#### CouchPotato (Organizes and obtains movies)
We will start by downloading the latest release of [Python 2.7](https://www.python.org/downloads/release/python-2715/) (*this should have already been installed with Tautulli*), [PyWin32 2.7](http://sourceforge.net/projects/pywin32/files/pywin32/Build%20217/) and [GIT](https://git-scm.com/). Run the Python installer and be sure to keep the default directory *C:\Python27*. Open up command prompt, type *cd C:\Tools* and then *git clone https://github.com/CouchPotato/CouchPotatoServer.git*. This will download the latest release for CouchPotato into *\CouchPotatoServer*.

Open command prompt and type "nssm install CouchPotato". Use the following settings for NSSM:

**Path:** 'C:\python27\python.exe'  
**Start directory:** 'C:\python27'  
**Arguments:** 'C:\Tools\CouchPotatoServer\couchpotato.py --data_dir=C:\Tools\CouchPotatoServer'  

![alt text](https://imgur.com/TNo4rsR.jpg "Couchpotato NSSM")  

Save the NSSM settings and when you're back the command prompt, type *nssm start CouchPotato*. Open a new browser tab and head to [127.0.0.1:5050](http://127.0.0.1:5050)
Port 38082 [127.0.0.1:38082/movies](http://127.0.0.1:38082/movies)

#### Sonarr (Organizes and obtains TV shows)
[Download](https://github.com/Sonarr/Sonarr/releases) the zip for the lateste release. Extact the contents to *C:\Tools\Sonarr*. Open command prompt and type *nssm install Sonarr*. Use the following settings for NSSM:

**Path:** 'C:\Tools\Sonarr\NzbDrone.exe'  
**Start directory:** 'C:\Tools\Sonarr'  

![alt text](https://imgur.com/WLGYMiX.jpg "Sonarr NSSM")  

Save the NSSM settings and when you're back the command prompt, type *nssm start Sonarr*. Open a new browser tab and head to [127.0.0.1:8989](http://127.0.0.1:8989)

Port 38083 [127.0.0.1:38083/shows](http://127.0.0.1:38083/shows)

#### Ombi (Allows users to request TV and Movies)
Let's start by [downloading](https://ombi.io/) Ombi, right-clicking on the zip file and check *Unblock*. Extract the contents of the zip file to *C:\Tools\Ombi*. Open a command prompt and type *nssm install Ombi*. Use the following settings for NSSM:

**Path:** 'C:\Tools\Ombi\Ombi.exe'  
**Start directory:** 'C:\Tools\Ombi'  

![alt text](https://imgur.com/rR6ACCf.jpg "Ombi NSSM")  

Save the NSSM settings and when you're back the command prompt, type *nssm start CouchPotato*. Open a new browser tab and head to [127.0.0.1:5000](http://127.0.0.1:5000)
Port 38084 [127.0.0.1:38084](http://127.0.0.1:38084)

#### Jackett (Aggregator)
[Download](https://github.com/Jackett/Jackett/releases)
**Path:** 'C:\ProgramData\Jackett\JackettConsole.exe'  
**Start directory:** 'C:\ProgramData\Jackett'  

![alt text](https://imgur.com/uQZXsg2.jpg "Jackett NSSM")  

Save the NSSM settings and when you're back the command prompt, type *nssm start Jackett*. Open a new browser tab and head to [127.0.0.1:9117](http://127.0.0.1:9117)
Port 38085 [127.0.0.1:38085](http://127.0.0.1:38085)

#### VPN
We will be using Private Internet Access as our VPN provider and OpenVPN for the connection. Download and install [OpenVPN](https://openvpn.net/index.php/open-source/downloads.html) with all defaults and the [PIA OVPN Files](https://www.privateinternetaccess.com/openvpn/openvpn.zip). Extract the contents of the zip file to *C:\Program Files\OpenVPN\config*.

**Path:** C:\Program Files\OpenVPN\bin\openvpn-gui.exe  
**Start directory:** C:\Program Files\OpenVPN\bin  
**Arguments:** --connect "<NAME OF CONFIG FILE>.ovpn"  
![alt text](https://imgur.com/xk5tWG7.jpg "OpenVPN NSSM")  

## Pi-Hole
This part of the project is not necessary, but it is something I wanted to do to block analytics, tracking, malware and other unwanted items as a result of ads. This will not only protect your computer but any device that connects to your network, be it a smart TV, smart phone, console etc. You can find out more about this project [here](https://pi-hole.net/).

## Final Remarks


