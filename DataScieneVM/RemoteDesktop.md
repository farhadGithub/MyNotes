# Remote Desktop from Windows to Ubuntu Using X11VNC

## Steps for full desktop on the server (Ubuntu machine):
On the Ubuntu desktop dash, type "Desktop Sharing" and open "Desktop Sharing Preferences" (From command line, this is equivalent of calling vino-preferences). Check "Allow other users to view your desktop", "Allow other user to control your desktop", "Requier the user to enter this password." Enter a password. Also select "Only when someone is connected." All these options can also be set from the command line. Press "Close."

Install x11vnc:
```
    sudo apt-get install x11vnc
```    
Create a password for your connection:    
```
    x11vnc -storepasswd  
``` 
After making a backup, replace your ~/.vnc/xstartup file with this:
```    
    #!/bin/sh

    export XKL_XMODMAP_DISABLE=1
    unset SESSION_MANAGER
    unset DBUS_SESSION_BUS_ADDRESS

    [ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
    [ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
    xsetroot -solid grey
    vncconfig -iconic &

    gnome-panel &
    gnome-settings-daemon &
    metacity &
    nautilus &
    gnome-terminal &
    gnome-session &
```
Then run:
```
    sudo x11vnc -xkb -noxrecord -noxfixes -noxdamage -display :0 -auth /var/run/lightdm/root/:0 -usepw
```    
Make note of the port that x11vnc is running from. Typically it is 5900. 
You can also check if your (x11) vnc server is running and at which port by running the following command:
```
    netstat -plant
```
and look for words vnc or vino in the list.    
## Steps for full desktop on the client (Windows machine):
Download Tight VNC Java Viewer from [here](http://www.tightvnc.com/download.php"). This viewer comes with SSH tunneling. Unzip the file and run `tightvnc-jviewer`. Enter the IP address or the hostname of the machine you want to connect to in the "Remote Host" box. Enter the port number you noted from the previous step. You can check "Use SSH tunneling" if you want SSH tunneling and have already set up a SSH server. 

