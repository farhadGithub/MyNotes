#Steps to create a Linux data science virtual machine (VM) on Microsoft Azure

This pages provides the details for creating an Ubuntu VM on Microsoft Azure for
data science and machine learning applications. In particular, this
document emphasizes on installing various Python packages appropriate
for such applications.

####1.  Provisioning an Ubuntu VM on Microsoft Azure:

a.  Log into your Azure account.

b.  Click on “New”.

c.  Click on “Compute”.

d.  Select an Ubuntu Server.
	
e.  Click on “Create” (Do not change the deployment model option).

f.  Fill in the requirement in the “Basics” panel (Instructions in
	the following steps assume you have chosen “Password” for
	“Authentication Type”).

g.  Choose a size in the next panel.

h.  Start the deployment.

####2.  Creating a DNS label name for VM:

a.  Once the deployment is completed, click on the IP address of your
    VM in the Azure portal.
	
b.  Click on “Configuration” in the “Setting” panel and enter your
    desired name.

####3.  Attaching a data disk (if needed):

a.  Click on “Setting” on your VM panel.

b.  Select “Disks”.

c.  Click on “Attach New” and choose a size.

d.  Connect to your VM (using a terminal emulator such as PuTTY). To
    do so, you can use the public IP address of your VM or the DNS
    name label you created in the last step.

e.  Follow the steps under “How to: Initialize a new data disk in
	Linux” on [this page](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-how-to-attach-disk/).

####4.  Adding a desktop environment to your VM:

a.  Run:

		sudo apt-get update
		sudo apt-get install xubuntu-desktop
		sudo apt-get install xfce4
		sudo apt-get install xfce4-terminal
		sudo apt-get install gnome-icon-theme-full tango-icon-theme
		
via command line to add [xfce4](http://www.xfce.org/) to your VM.

####5. Setting up a remote desktop connection for your VM:

a.  Go to your Azure portal and under “All resources” and select
    “Network Security Group”.

b.  Select “Inbound Security Rules” in the “Settings” panel.

c.  Click on “Add” and add a rule with name: rxdp, protocol: TCP,
    destination port range: 3389 while keeping other options at
    their default value.

d.  Set up a remote desktop server on your VM by following the steps
    described [here]
    (http://www.tweaking4all.com/software/linux-software/use-xrdp-remote-access-ubuntu-14-04/).
    In short you need to run the following commands:
        
		
        sudo apt-get install xrdp
		echo xfce4-session > ~/.xsession
		sudo nano /etc/xrdp/startwm.sh

The content of ```startwm.sh``` should be as follows:

		#!/bin/sh
		if [ -r /etc/default/locale ]; then
			. /etc/default/locale
			export LANG LANGUAGE
		fi	
		startxfce4
Finally run:
		
		sudo service xrdp restart

e.  Run a remote desktop connection and use the public IP of your VM to
    connect to your VM.

####6. Installing git

a.  Run:

		sudo apt-get install git

####7. Installing Azure Command Lines (Required to interact with other Azure services, e.g. Azure Blob Storage):

a.  To install Azure Command Lines, run:
        
		sudo apt-get update
		sudo apt-get install nodejs
		sudo apt-get install npm
		sudo npm install azure-cli -g
		
b.  To transfer files from and to Azure Blob Storage, go to your
    Azure Blob Storage account on Azure Portal and copy the “KEY1”
    from “Connection strings” and run the following commands:
        
		AZURE\_STORAGE\_CONNECTION\_STRING=key1
		azure storage blob upload
or
		
		azure storage blob download

Both commands will ask you for the name of the container on the Azure
Blob Storage account and the name of your file.

c.  To interact with Azure Blob Storage from Python, read this [link](http://blogs.msdn.com/b/tconte/archive/2013/04/17/how-to-interact-with-windows-azure-blob-storage-from-linux-using-python.aspx)
    

####8.  Installing Anaconda (Based on instructions outlined [here](http://docs.continuum.io/anaconda/install#linux-install)):

a.  Make a remote desktop connection to your VM and download the
    Anaconda installer for Linux from this
    [link](https://www.continuum.io/downloads) in your VM browser.

b.  Run:
      
		sudo bash \~/Downloads/Anaconda2-2.4.1-Linux-x86\_64.sh

The name of the file might be different depending on what version of
the Anaconoda you download.

c.  The script will start Python. When asked to choose a location, you
    can enter a global location (e.g. ```/user/```) to install Anaconda for
    all users on VM.
