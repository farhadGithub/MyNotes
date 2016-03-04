#Steps to create an Ubuntu Linux data science virtual machine (VM) on Microsoft Azure

This pages provides the details for creating an Ubuntu VM on Microsoft Azure for
data science and machine learning applications. In particular, this
document emphasizes on installing various Python packages appropriate
for such applications.

####1.  Provisioning an Ubuntu VM on Microsoft Azure:

a.  Log into your Azure account.

b.  Click on **New**.

c.  Click on **Compute**.

d.  Select an Ubuntu Server.
	
e.  Click on **Create** (Do not change the deployment model option).

f.  Fill in the requirement in the “Basics” panel (Instructions in
	the following steps assume you have chosen **Password** for
	**Authentication Type**).

g.  Choose a size in the next panel.

h.  Start the deployment.

####2.  Creating a DNS label name for VM:

a.  Once the deployment is completed, click on the IP address of your
    VM in the Azure portal.
	
b.  Click on **Configuration** in the **Setting** panel and enter your
    desired name.

####3.  Attaching a data disk [optional]:

a.  Click on **Setting** on your VM panel.

b.  Select **Disks**.

c.  Click on **Attach New** and choose a size.

d.  Connect to your VM (using a terminal emulator such as PuTTY). To
    do so, you can use the public IP address of your VM or the DNS
    name label you created in the last step.

e.  Follow the steps under **How to: Initialize a new data disk in
	Linux** on [this page](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-how-to-attach-disk/).

####4.  Adding a desktop environment to your VM [optional]:

a.  Run:

		sudo apt-get update
		sudo apt-get install xubuntu-desktop
		sudo apt-get install xfce4
		sudo apt-get install xfce4-terminal
		sudo apt-get install gnome-icon-theme-full tango-icon-theme
		
via command line to add [xfce4](http://www.xfce.org/) to your VM.

####5. Setting up a remote desktop connection for your VM [optional]:

a.  Go to your Azure portal and under **All resources** and select
    **Network Security Group**.

b.  Select **Inbound Security Rules** in the **Settings** panel.

c.  Click on **Add** and add a rule with name: rxdp, protocol: TCP,
    destination port range: 3389 while keeping other options at
    their default value.

d.  Set up a remote desktop server on your VM by following the steps
    described [here]
    (http://www.tweaking4all.com/software/linux-software/use-xrdp-remote-access-ubuntu-14-04/).
    In short you need to run the following commands:
        
		
        sudo apt-get install xrdp
	echo xfce4-session > ~/.xsession
	sudo nano /etc/xrdp/startwm.sh

The content of `startwm.sh` should be as follows:

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
    
f. If you want to always connect to the same session when you connect to your VM via remote desktop, als run:

		 sudo vim /etc/xrdp/xrdp.ini

And change the following lines:

		[xrdp1]
		name=sesman-vnc
		lib=libvnc.so
		username=ask
		password=ask
		ip=127.0.0.1
		port=-1

to

		[xrdp1]
		name=sesman-vnc
		lib=libvnc.so
		username=ask
		password=ask
		ip=127.0.0.1
		port=ask5910

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
    Azure Blob Storage account on Azure Portal and copy the **KEY1**
    from **Connection strings** and run the following commands:
        
		AZURE\_STORAGE\_CONNECTION\_STRING=key1
		azure storage blob upload
or
		
		azure storage blob download

Both commands will ask you for the name of the container on the Azure
Blob Storage account and the name of your file.

c.  To interact with Azure Blob Storage from Python, read this [link](http://blogs.msdn.com/b/tconte/archive/2013/04/17/how-to-interact-with-windows-azure-blob-storage-from-linux-using-python.aspx).
    

####8.  Installing Anaconda (Based on instructions outlined [here](http://docs.continuum.io/anaconda/install#linux-install)):

a.  From the Anaconda website, copy and paste the link address of its Linux installer and then run:
		
		wget "link address here"

Your command after pasting should look like this:

		wget "https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda2-2.5.0-Linux-x86_64.sh"

b.  Run:
      
		sudo bash Anaconda2-2.5.0-Linux-x86_64.sh

The name of the file might be different depending on what version of
the Anaconoda you download.

c.  Assuming you have installed Anaconda in its default path which is your home directory, run:
		
		cd anaconda2
		source bin/activate ~/anaconda2
		
####8.  Accessing ipython Notebook Server from a Client Brower (Based on instructions outlined [here](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)):

 a. If you would have a password to access your notebooks, follow these steps:
		
		python
		In [1]: from notebook.auth import passwd
		In [2]: passwd()
		Enter password:
		Verify password:
		Out[2]: 'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'
	
b. Then we need to make some modifications to `jupyter_notebook_config.py`. This file should be located at `~/.jupyter`. If this does not exit in your system, run the following command to generate a default one:
	
		jupyter notebook --generate-config
	
Then uncomment, add or change the following lines to `jupyter_notebook_config.py` in a text editor:
	
		c.NotebookApp.ip = '*'
		c.NotebookApp.open_browser = False
		c.NotebookApp.port = 8888

If you created a hased password as outlined in step 1, you also need to add it to  `jupyter_notebook_config.py`:

		c.NotebookApp.password = u'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'

c. If you also would like to add SSL for more security when your browser sends your password to the VM, follow these steps:
 
 		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mykey.key -out mycert.pem
 
 And uncomment, add or change the following lines to `jupyter_notebook_config.py` in a text editor:	
 
 		c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'
 		c.NotebookApp.keyfile = u'/absolute/path/to/your/certificate/mykey.key'

d. Finally run `ipython notebook` on your VM to start the notebook server. In order to connect to your notebooks from your client, enter `http:\\vm_ip_addres:8888/tree` in your browser if you have not enabled SSL or `https:\\vm_ip_addres:8888/tree` if you have done so.
