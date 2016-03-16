#Steps to create an Ubuntu Linux data science virtual machine (VM) on Microsoft Azure

This pages provides the details for creating an Ubuntu VM on Microsoft Azure for
data science and machine learning applications. 

####1.  Creating an Ubuntu VM on Microsoft Azure:

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

####2.  Configuring Ports:

After deployment is completed,

a. Click on **Resource Groups**.

b. Click on your VM resource group.

c. Click on **Network Security Group**.

d. Click on **Inbound security rules**.

e. You should see a rule already set up for port 22 by default. Add two new rules for ports 3389 (xrdp) and 8888 (notebooks) by clicking on **Add** and entering the port number in the **Destination port range** and assinging a name to your rule. Other options can have their default values.  

####3.  Creating a DNS Label Name for VM:

After deployment is completed,

a. Click on **Resource Groups**.

b. Click on your VM resource group.

c. Click on **Virtual machine**.
	
d.  In the **Setting** panel, click on **Public IP address/DNS label name** and enter your
    desired name for your VM.

####4.  Connecting to Your VM:

a.  On Mac or Linux, simply type `ssh VM_DNS_label_name` to connect to your VM.
	
b.  On Windows, you can download and install [Putty](http://www.putty.org/). On the Putty panel, you can then enter the DNS name or the IP address of your VM. Usually for the first connection, you need to enter the IP address.

c.  These two pages ([here](http://dag.wiee.rs/blog/content/improving-putty-settings-on-windows) and [here](http://looselytyped.blogspot.com/2013/02/zenburn-pleasant-color-scheme-for-putty.html)) provide some useful tips to customize the color, font and other attributes of your Putty client. Here is a summary:

		Window\Colours:
		Default Foreground - 255/255/255
		Default Background - 51/51/51
		ANSI Black - 77/77/77
		ANSI Green - 152/251/152
		ANSI Yellow - 240/230/140
		ANSI Blue - 205/133/63
		ANSI Blue Bold -135/206/235
		ANSI Magenta - 255/222/173 or 205/92/92
		ANSI Cyan - 255/160/160
		ANSI Cyan Bold - 255/215/0
		ANSI White - 245/222/179
		
		Window\Appearanc\Font Settings:
		Consolas, 10-point
		
		Window\Lines of scrollback:
		20000
	
####5.  Attaching a Data Disk:

After deployment is completed,

a. Click on **Resource Groups**.

b. Click on your VM resource group.

c. Click on **Virtual machine**.
	
d.  In the **Setting** panel, select **Disks**.

e.  Click on **Attach New** and choose a size.

f.  Connect to your VM (using a terminal emulator such as PuTTY). To
    do so, you can use the public IP address of your VM or the DNS
    name label you created in the last step.

g.  Follow the steps under **How to: Initialize a new data disk in
	Linux** on [this page](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-how-to-attach-disk/). Mainly, you need to do as follows after connecting to your VM. Run:
        
        lsscsi 

To find out the device id. If `lsscsi` is not installed, run: 

        sudo apt-get install lsscsi

In most cases, your new disk is identified by `/dev/sdc`. Assuming, this is the case, run:

        sudo fdisk /dev/sdc


You may need to run `sudo` with the `-i` option. Answer the questions raised by `fdisk` according to what outlined in the [link](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-how-to-attach-disk/). Then run:

        sudo mkfs -t ext4 /dev/sdc1

Create a directory to link to the disk:

        sudo mkdir /datadrive
        sudo -i blkid

Note the `UUID` of `/dev/sdc1`. Then edit `/etc/fstab` by adding a line similar to this one:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
 

####6.  Updating Your Software Managenet Tool:
	
		sudo apt-get update


####7.  Adding a Desktop Environment to Your VM [optional]:

Run the following commands to add [xfce4](http://www.xfce.org/) to your VM:

		sudo apt-get install xubuntu-desktop
		sudo apt-get install xfce4
		sudo apt-get install xfce4-terminal
		sudo apt-get install gnome-icon-theme-full tango-icon-theme
		
####8. Setting up a Remote Desktop Connection for Your VM [optional]:
Set up a remote desktop server on your VM by following the steps
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

Run a remote desktop connection and use the public IP of your VM to connect to your VM.
    
If you want to always connect to the same session when you connect to your VM via remote desktop, als run:

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

####9. Installing Git:

		sudo apt-get install git

####10. Installing Node.js:

		sudo apt-get install nodejs
		sudo apt-get install npm

####11. Installing Azure Command Lines (Required to interact with other Azure services, e.g. Azure Blob Storage):

a.  To install Azure Command Lines, run:
        
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
    
####12.  Installing Anaconda for Python 2.7 (Based on instructions outlined [here](http://docs.continuum.io/anaconda/install#linux-install)):

a.  From the Anaconda website, copy and paste the link address of its Linux installer and then run:
		
		wget "link address here"

Your command after pasting should look like this:

		wget "https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda2-2.5.0-Linux-x86_64.sh"

b.  Run:
      
		bash Anaconda2-2.5.0-Linux-x86_64.sh

The name of the file might be different depending on what version of
the Anaconoda you download.

c.  Assuming you have installed Anaconda in its default path which is your home directory, run:
		
		source ~/.bashrc
		cd anaconda2
		source activate ~/anaconda2
		
####13.  Accessing IPython Notebook Server from a Client Browser (Based on instructions outlined [here](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)):

 a. If you would like to have a password to access your notebooks, follow these steps:
		
		python
		In [1]: from notebook.auth import passwd
		In [2]: passwd(algorithm='sha224')
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

d. Finally run `jupyter notebook` on your VM to start the notebook server. In order to connect to your notebook server from your client, enter `http:\\vm_ip_addres:8888/tree` in your browser if you have not enabled SSL or `https:\\vm_ip_addres:8888/tree` if you have done so.

####14.  Adding a Python 3.5 Environement to Anaconda:

		conda create -n python35 python=3.5 anaconda
		source activate python35

####15.  Adding Python 2.7 Environement to Anaconda:

		conda create -n python27 python=2.7 anaconda
		source activate python27

####16.  Creating Both Python 2.7 and 3.5 Notebooks:

		source activate python35
		conda install notebook ipykernel
		jupyter kernelspec install-self --user
		source activate python27
		conda install notebook ipykernel
		jupyter kernelspec install-self --user
		
Regardless of the environment in which you are, by running `jupyter notebook`, you should be able to create both python 2.7 and 3.5 notebooks.

####17.  Adding the Jupyter server as a start-up service:

		cd /etc/init.d
		echo "cd ${HOME};setuid ${USER} nohup ${USER}/anaconda2/bin/ipython notebook > /tmp/t.log 2>&1 < /dev/null &" > start_ipython
		sudo chmod +x /etc/init.d/start_ipython 
		sudo update-rc.d start_ipython defaults
		
Finally, we need to reboot to start the service

		sudo reboot
		
And if you want to stop this service, run:

		sudo update-rc.d -f start_ipython remove
		
####18.  Installing [Microsoft R Open](https://mran.revolutionanalytics.com/download/):
We assume in these steps that the VM is running on Ubuntu 14:

		wget 'https://mran.revolutionanalytics.com/install/mro/3.2.3/MRO-3.2.3-Ubuntu-14.4.x86_64.deb'
		sudo dpkg -i MRO-3.2.3-Ubuntu-14.4.x86_64.deb

If you see a complaint about a dependency issue with some packages, run:
	
		sudo apt-get install package-name 	
		
####19.  Adding R Kernel to Jupyter server:

		conda update ipython 
		conda update pyzmq
		#sudo apt-get install libzmq3-dev python-zmq
		
Run:

		sudo R
		install.packages("devtools")
		install.packages("RCurl")
		library(devtools)
		install.packages(c('rzmq','repr','IRkernel','IRdisplay'),repos = c('http://irkernel.github.io/', getOption('repos')))
		q()
		
		R
		IRkernel::installspec()

When installing `devtools` in R, you may also exit R and install other Linux packages.

And then start the server (i.e. `jupyter notebook`).		



