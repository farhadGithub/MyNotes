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
    
####12.  Installing Anaconda Python 2.7 (Based on instructions outlined [here](http://docs.continuum.io/anaconda/install#linux-install)):

a.  From the Anaconda website, copy and paste the link address of its Linux installer and then run:
		
		wget "link address here"

Your command after pasting should look like this:

		wget "http://repo.continuum.io/archive/Anaconda2-4.0.0-Linux-x86_64.sh"

b.  Run:
      
		bash Anaconda2-4.0.0-Linux-x86_64.sh

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


####18.  Adding the Jupyter server as a start-up service:

		cd ~
		echo "cd ${HOME};setuid ${USER} nohup ${HOME}/anaconda2/bin/ipython notebook > /tmp/t.log 2>&1 < /dev/null &" > start_ipython
		sudo mv start_ipython /etc/init.d  
		cd /etc/init.d
		sudo chmod +x /etc/init.d/start_ipython 
		sudo update-rc.d start_ipython defaults
		
Finally, we need to reboot to start the service

		sudo reboot
		
And if you want to stop this service, run:

		sudo update-rc.d -f start_ipython remove
		
####19.  Installing [Microsoft R Open](https://mran.revolutionanalytics.com/download/):
We assume in these steps that the VM is running on Ubuntu 14:

		wget 'https://mran.revolutionanalytics.com/install/mro/3.2.4/MRO-3.2.4-Ubuntu-14.4.x86_64.deb'
		sudo dpkg -i MRO-3.2.4-Ubuntu-14.4.x86_64.deb

If you see a complaint about a dependency issue with some packages, run:
	
		sudo apt-get install package-name 	
		
####20.  Adding R Kernel to Jupyter server:

		conda update ipython 
		conda update pyzmq
		#sudo apt-get install libzmq3-dev python-zmq
		
		sudo R
		install.packages("devtools")
		install.packages("RCurl")
		library(devtools)
		install.packages(c('rzmq','repr','IRkernel','IRdisplay'),repos = c('http://irkernel.github.io/', getOption('repos')))
		q()
		
		R
		IRkernel::installspec()

When installing `devtools` in R, you may also need to exit R and install other Linux packages.

And then start the server (i.e. `jupyter notebook`).	

####21.  Installing [Keras](http://keras.io/):

		sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
		pip install keras

		
####22. Installing Screen:

		sudo apt-get install screen

####23. Installing CUDA Toolkit (Based on instructions outlined [here](http://askubuntu.com/questions/799184/how-can-i-install-cuda-on-ubuntu-16-04)):

If you install from scartch, the following commands most likely will work:

a. Install packages:

	sudo apt-get install -y build-essential cmake cmake-qt-gui git pkg-config
	sudo apt-get install nvidia-361-updates nvidia-361-updates-dev
	sudo apt-get install nvidia-prime
	sudo apt-get install nvidia-profiler
	sudo apt-get install nvidia-settings
	sudo apt-get install nvidia-visual-profiler
	sudo apt-get install nvidia-cuda-toolkit

However, if you are upgrading, follwow these lines:

a. [Download](https://developer.nvidia.com/cuda-downloads) CUDA runfile (local).

b. Check the md5 sum: 

	md5sum cuda_7.5.18_linux.run 
	
Only continue if it is correct.

c. Remove any other installation:

	sudo apt-get purge nvidia-cuda* 
	
If you want to install the drivers too, then 
	
	sudo apt-get purge nvidia-*
	
d. If you want to install the display drivers(*), logout from your GUI. Go to a terminal session (ctrl+alt+F2)

	sudo service lightdm stop
ls

e. 

	sudo sh cuda_7.5.18_linux.run --override 
	
Make sure that you say y for the symbolic link.

f. Start lightdm again: 
	
	sudo service lightdm start

Test the device and the toolkit version:

	nvidia-smi
	
	nvcc --version

It is important to note that CUDA toolkit version 8 by default is installed at Please make sure that

	/usr/local/cuda-8.0/bin

So this should be taken into account when installing other tooks that use this toolkit.

####23. Installing TensorFlow:

		sudo apt-get install python-pip python-dev
		
For CPU:		
	
		pip install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.8.0-cp27-none-linux_x86_64.whl

For GPU Only:

		pip install --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-0.8.0-cp27-none-linux_x86_64.whl

Add the following lines to the ```.bashrc``` file:

		export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64"
		export CUDA_HOME=/usr/local/cuda

####24. Installing Caffe:

a. 		

		sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
		sudo apt-get install --no-install-recommends libboost-all-dev
		sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
		sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
		sudo apt-get install -y libatlas-base-dev
		sudo apt-get install -y --no-install-recommends libboost-all-dev
		sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev

		
b.

		git clone https://github.com/BVLC/caffe.git
		cd caffe
		cp Makefile.config.example Makefile.config
		
c. Uncomment appropriate lines on ```Makefile.config```

		## Refer to http://caffe.berkeleyvision.org/installation.html
		# Contributions simplifying and improving our build system are welcome!
		
		# cuDNN acceleration switch (uncomment to build with cuDNN).
		USE_CUDNN := 1
		
		# CPU-only switch (uncomment to build without GPU support).
		# CPU_ONLY := 1
		
		# uncomment to disable IO dependencies and corresponding data layers
		USE_OPENCV := 0
		# USE_LEVELDB := 0
		# USE_LMDB := 0
		
		# uncomment to allow MDB_NOLOCK when reading LMDB files (only if necessary)
		#       You should not set this flag if you will be reading LMDBs with any
		#       possibility of simultaneous read and write
		# ALLOW_LMDB_NOLOCK := 1
		
		# Uncomment if you're using OpenCV 3
		OPENCV_VERSION := 3
		
		# To customize your choice of compiler, uncomment and set the following.
		# N.B. the default for Linux is g++ and the default for OSX is clang++
		# CUSTOM_CXX := g++
		
		# CUDA directory contains bin/ and lib/ directories that we need.
		#CUDA_DIR := /usr/local/cuda
		# On Ubuntu 14.04, if cuda tools are installed via
		# "sudo apt-get install nvidia-cuda-toolkit" then use this instead:
		CUDA_DIR := /usr
		
		# CUDA architecture setting: going with all of them.
		# For CUDA < 6.0, comment the *_50 lines for compatibility.
		CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
		                -gencode arch=compute_20,code=sm_21 \
		                -gencode arch=compute_30,code=sm_30 \
		                -gencode arch=compute_35,code=sm_35 \
		                -gencode arch=compute_50,code=sm_50 \
		                -gencode arch=compute_50,code=compute_50
		
		# BLAS choice:
		# atlas for ATLAS (default)
		# mkl for MKL
		# open for OpenBlas
		BLAS := atlas
		# Custom (MKL/ATLAS/OpenBLAS) include and lib directories.
		# Leave commented to accept the defaults for your choice of BLAS
		# (which should work)!
		# BLAS_INCLUDE := /path/to/your/blas
		# BLAS_LIB := /path/to/your/blas
		
		# Homebrew puts openblas in a directory that is not on the standard search path
		# BLAS_INCLUDE := $(shell brew --prefix openblas)/include
		# BLAS_LIB := $(shell brew --prefix openblas)/lib
		
		# This is required only if you will compile the matlab interface.
		# MATLAB directory should contain the mex binary in /bin.
		# MATLAB_DIR := /usr/local
		# MATLAB_DIR := /Applications/MATLAB_R2012b.app
		
		# NOTE: this is required only if you will compile the python interface.
		# We need to be able to find Python.h and numpy/arrayobject.h.
		#PYTHON_INCLUDE := /usr/include/python2.7 \
		#               /usr/lib/python2.7/dist-packages/numpy/core/include
		# Anaconda Python distribution is quite popular. Include path:
		# Verify anaconda location, sometimes it's in root.
		ANACONDA_HOME := $(HOME)/anaconda2
		PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
		                  $(ANACONDA_HOME)/include/python2.7 \
		                  $(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include \
		
		# Uncomment to use Python 3 (default is Python 2)
		# PYTHON_LIBRARIES := boost_python3 python3.5m
		# PYTHON_INCLUDE := /usr/include/python3.5m \
		#                 /usr/lib/python3.5/dist-packages/numpy/core/include
		
		# We need to be able to find libpythonX.X.so or .dylib.
		#PYTHON_LIB := /usr/lib
		PYTHON_LIB := $(ANACONDA_HOME)/lib
		
		# Homebrew installs numpy in a non standard path (keg only)
		# PYTHON_INCLUDE += $(dir $(shell python -c 'import numpy.core; print(numpy.core.__file__)'))/include
		# PYTHON_LIB += $(shell brew --prefix numpy)/lib
		
		# Uncomment to support layers written in Python (will link against Python libs)
		# WITH_PYTHON_LAYER := 1
		
		# Whatever else you find you need goes here.
		INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
		LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib
		
		# If Homebrew is installed at a non standard location (for example your home directory) and you use it for general dependencies
		# INCLUDE_DIRS += $(shell brew --prefix)/include
		# LIBRARY_DIRS += $(shell brew --prefix)/lib
		
		# Uncomment to use `pkg-config` to specify OpenCV library paths.
		# (Usually not necessary -- OpenCV libraries are normally installed in one of the above $LIBRARY_DIRS.)
		# USE_PKG_CONFIG := 1
		
		# N.B. both build and distribute dirs are cleared on `make clean`
		BUILD_DIR := build
		DISTRIBUTE_DIR := distribute
		
		# Uncomment for debugging. Does not work on OSX due to https://github.com/BVLC/caffe/issues/171
		# DEBUG := 1
		
		# The ID of the GPU that 'make runtest' will use to run unit tests.
		TEST_GPUID := 0
		
		# enable pretty build (comment to see full commands)
		Q ?= @

d. 

		make clean
		make all
		make test
		make runtest
	
e. If encountered a ``` __mempcpy_inline(void*, const void*, size_t)’``` error, edit ```Makefile``` and replace:

		NVCCFLAGS += -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
with

		NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)

f.

		cd ~/caffe/python
		for req in $(cat requirements.txt); do pip install $req; done
	
g.

		cd ~/caffe/
		make pycaffe
	
h. Add the following line to ```.bashrc```

		export PYTHONPATH=~/caffe/python:$PYTHONPATH
		export LD_LIBRARY_PATH="/usr/local/cuda/lib64:/home/faghasse/anaconda2/lib:$LD_LIBRARY_PATH"

i. 

		source .bashrc
	
j.

		python
		import caffe
	
k. Call ```Caffe``` command line from this directory:

		~/caffe/build/tools/caffe

####25. Installing ssh:

a. Install the package:

		sudo apt-get install openssh-server
		
b. Start the server:

		sudo restart ssh
		sudo systemctl restart ssh

c. Test the server:

		ssh -v localhost


d. Open port 22 on the firewall if needed:

	sudo ufw status
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	sudo ufw allow ssh
	sudo ufw allow 2222/tcp
	sudo ufw allow www
	sudo ufw enable

####26. Installing vim:

	sudo install vim
	
####28. Installing opencv:

a. Get the packages:

	git clone https://github.com/Itseez/opencv.git
	git clone https://github.com/Itseez/opencv_contrib.git

b. Set the flags in CMake GUI:

 	- CMAKE_BUILD_TYPE => Release
  	- OPENCV_EXTRA_MODULES_PATH => /home/faghasse/opencv_contrib/modules
  	- CMAKE_INSTALL_PREF => /usr/local
  	
	- PYTHON2_EXECUTABLE => ~/anaconda2/bin/python
	- PYTHON2_INCLUDE_DIR => ~/anaconda2/include/python2.7
	- PYTHON2_INCLUDE_DIR2  =>
	- PYTHON2_LIBRARY  => ~/anaconda2/lib/libpython2.7.so
	- PYTHON2_LIBRARY_DEBUG  =>
	- PYTHON2_NUMPY_INCLUDE_DIRS  => ~/anaconda2/lib/python2.7/site-packages/numpy/core/include
	- PYTHON2_PACKAGES_PATH  => ~/anaconda2/lib/python2.7/site-packages

	- PYTHON3_EXECUTABLE  => ~/anaconda2/envs/python34/bin/python
	- PYTHON3_INCLUDE_DIR  => ~/anaconda2/envs/python34/include/python3.4m
	- PYTHON3_INCLUDE_DIR2  =>
	- PYTHON3_LIBRARY  => ~/anaconda2/envs/python34/lib/libpython3.4m.so
	- PYTHON3_LIBRARY_DEBUG  =>
	- PYTHON3_NUMPY_INCLUDE_DIRS  => ~/anaconda2/envs/python34/lib/python3.4/site-packages/numpy/core/include
	- PYTHON3_PACKAGES_PATH  => ~/anaconda2/envs/python34/lib/python3.4/site-packages

c. If you encouter an hdf5 error when compling python, you need to modify the file ```/opencv_contrib-3.1.0/modules/hdf/include/opencv2/hdf/hdf5.hpp`` in line 40:

	--- #include
	+++ #include "/usr/include/hdf5/serial/hdf5.h"

d.  	

	sudo make install
	sudo ldconfig

####28. Installing cudnn:

a. Download the library from this address:

	https://developer.nvidia.com/cudnn
	
b.

	tar xvf cudnn-8.0-linux-x64-v5.1.tgz
	sudo cp -av cuda/lib64/* /usr/lib/x86_64-linux-gnu/
	sudo cp -av cuda/include/* /usr/include/






	
