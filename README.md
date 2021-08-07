# Private-PSO
Tools and instructions to play Phantasy Star Online (GC Version)

# How to setup the server

When I started this, I thought it was going to be more work that it was, but it seems like the community has done most of the heavy lifting for me.  So I will be referencing forked repositories.  

## Easiest way to get started:
1. On the PC that you want to be your Server, [install Docker](https://docs.docker.com/engine/install/).
2. [Install Git](https://git-scm.com/downloads)
3. Open a command line terminal of your choice
    a. Windows = Powershell / CMD
    b. Linux = Bash (or whatever you want)
4. Clone [Gered](https://github.com/gered)'s repository by typing:
```
gitt clone https://github.com/thedancingfetus/psoserv_fuzziqer_docker.git
```
5. Build the the docker container by typing:
```
cd psoserv_fuzziqer
docker build -t gered/psoserv_fuzziqer:latest
```
6. Setup your myconfig.json:
    * Open config.json in any text editor
    * Edit the value for ServerName (Example: "ServerName": "VALUE_TO_EDIT") to what you want the server to be called.
    * Edit LocalAddress to the IP Address of the local machine.
        - To get IP on Windows typing ipconfig in the Command Line.  You can have multiple network adapters, but you will most likely want the one that has a Default Gateway specified.  
        - Linux, check with your distro documentaiton on how to get your IP Address.
    * Edit ExternalAddress to the IP that will be used for remote connections.
        - If you plan on having people connect from the internet, this would be the Internet IP Address of the machine.  An easy way for figuring that out is going to [WhatIsMyIp.com](https://www.whatismyip.com/).  For linux you could run this command: curl ifconfig.me .
    * Save As myconfig.json in the same folder as config.json that you opened at the start.  
7. Create a file to save your user License data:
    * Windows, in the Powershell session you used to build the container, just run New-Item -Type File -Path .\licenses.nsi
    * Linux, you can run touch ./licenses.nsi
8. Verify your Folder and Files
    * Check to see you have a myconfig.json
    * Check to see you have a licenses.nsi
9. Start the server Windows:
```
docker run `
	--rm `
	-d -it `
	--name psoserv_fuzziqer `
	-p 9000:9000 `
	-p 9001:9001 `
	-p 9003:9003 `
	-p 9100:9100 `
	-p 9103:9103 `
	-p 9200:9200 `
	-p 9201:9201 `
	-p 9300:9300 `
	-p 10000:10000 `
	-p 11000:11000 `
	-p 12000:12000 `
	-p 12004:12004 `
	-p 12005:12005 `
	-p 12008:12008 `
	-p 9420:9420 `
	-p 9421:9421 `
	-p 9422:9422 `
	-p 53:53/udp `
	-v ${PWD}/myconfig.json:/newserv/system/config.json `
    -v ${PWD}/licenses.nsi:/newserv/system/licenses.nsi `
	gered/psoserv_fuzziqer:latest
```
Linux:
```
docker run \
	--rm \
	-d -it \
	--name psoserv_fuzziqer \
	-p 9000:9000 \
	-p 9001:9001 \
	-p 9003:9003 \
	-p 9100:9100 \
	-p 9103:9103 \
	-p 9200:9200 \
	-p 9201:9201 \
	-p 9300:9300 \
	-p 10000:10000 \
	-p 11000:11000 \
	-p 12000:12000 \
	-p 12004:12004 \
	-p 12005:12005 \
	-p 12008:12008 \
	-p 9420:9420 \
	-p 9421:9421 \
	-p 9422:9422 \
	-p 53:53/udp \
	-v ${PWD}/myconfig.json:/newserv/system/config.json \
    -v ${PWD}/licenses.nsi:/newserv/system/licenses.nsi \
	gered/psoserv_fuzziqer:latest
```
10. Verify network ports are open and forwarded
    * All ports must be open on the Servers firewall and if you want external connections from the internet, you will need to configure your device to allow those ports inbound.  All are TCP except for 53, which is a UDP port. 

## Add User Licenses
You can choose to allow Unregistered users to connect, but then anyone that has your Server's IP would be able to connect.  If you want to enable this, set AllowUnregisteredUsers to true in the myconfig.json file.  To add a user run the following command:
```
docker attach psoserv_fuzziqer
add-license serial=<10 Digit Serial Number> access-key=<12 Digit Access Key> gc-password=<8 character or less String Password> 
```
Example: add-license serial=2341347765 access-key=479248730295 gc-password=Pass

These values can be given to you by your players from their GameCube, or you can make them up and give them to the player you want to connect.  

# How to setup Dolphin-Emu to connect to your Private PSO Server

## Windows 10
1. Download [OpenVPN version 2.4.8](https://swupdate.openvpn.org/community/releases/openvpn-install-2.4.8-I602-Win10.exe)
2. Install OpenVPN, but only have the Tap Virtual Ethernet Adapter selected on the list of components to install.
3. Open Control Panel -> Network and Sharing Center -> Change adapter settings
    * To get to Control Panel, you should be able to click start, and type Control Panel.  It should be the first option.
4. Here you should see your Network Interface that is connected to the internet or your Home network and a interface call Local Area Connection with TAP-Windows Adapter V9 underneath it.  We need to bridge these two interface.
    * Hold down the Ctrl key and click on your Network Interface attached to your network and the Tap V9 interface.  Then right click on one of the hilighted network adapters and click on the Bridge option.  
        - It is not unusual to temporarily lose internet connection while the Bridge is being built.  It should return in about 30 seconds. 
5. Setup Dolphin:
    * Download the latest [Dolphin Emulator](https://dolphin-emu.org/download/). I use the latest Beta Versions and they seem to work fine.  
    * Unzip the emulator to a location on your PC and launch Dolphin.exe
    * Click on Config on the Top Graphical Menu
    * Select GameCube
        - Slot A: hit Dropdown and select Memory Card
        - SP1: hit Dropdown and Select Broadband Adapter (TAP)
6. Dolphin should now be configured to support PSO Online play.
7. Click close on the Menu and then Select Controllers on the Top Graphical menu.
8. For Controller 1 select Standard Controller, then Configure.  This will let you set your bindings.
9. For Controller 2 select Keyboard and do your bindings.  This will let you Type text in PSO with your keyboard, so you don't have to use the On-Screen keyboard.  

## How to setup PSO
1. Launch your Phantasy Star Online GameCube rom of your choice.
2. Hit start, go to Options, select Network Option, and then select Provider Option.  When it prompts, select Yes.
3. You can say yes to all the windows that pop up until you get to a screen that is the Network Setup Menu.  This menu is a little buggy at displaying what you have typed.  If you select a letter, try moving your currsor arround with the Analog stick until it registers:
    * Select Network Setup
    * Edit Menu
    * Give your connection a Name and select Next
    * Select Automatically obtain an IP Address (DHCP) and select Do not automatically disconnect
        - Again, sometimes it doesn't register visually your selections.  Just click Next and then Back to verify your settings saved.
    * Select Manual, and then put in the IP Address of the Private PSO server you are going to be connecting to, then click Next.  Example: 43.15.126.49
    * Don't set anything on the Browser settings page and just click Next.
    * Click Save
    * Click Return to Game
4. You should now be able to connect to your Custom PSO Server.  If you have not been given a serial, access key, and password by your Server Admin, just set one while building your character and send it to the Admin so it can be added to the server.  