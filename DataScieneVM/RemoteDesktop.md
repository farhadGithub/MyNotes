# Remote Desktop from Windows to Ubuntu Using VNC and X11VNC

*. Although Ubuntu comes with a desktop sharing tool, I was not able to get to work. Instead, here I describe two approaches 
to set up a light-weight desktop and a full desktop. We use tightvncserver for light connection and x11vnc for full connection.

## Common steps for both light-weight and full desktops on the server:
On Ubuntu desktop dash, type "Desktop Sharing" and open "Desktop Sharing Preferences" (From command line, this is equivalent of calling vino-preferences). Check "Allow other users to view your desktop", "Allow other user to control your desktop", "Requier the user to enter this password." Enter a password. Also select "Only when someone is connected." All these options can also be set from the command line. Press "Close."

## Steps for full desktop on the server:
1. Run the following commands:
  
Install x11vnc:

    sudo apt-get install x11vnc
    
Create a password for your connection (This might be redundant step as previously done by envoking vino-preferences:    

    x11vnc -storepasswd  
    
Then run:

    sudo x11vnc -xkb -noxrecord -noxfixes -noxdamage -display :0 -auth /var/run/lightdm/root/:0 -usepw
    
2. You can always check if your (x11) vnc server is running and at which port by running the following command:

```
    netstat -plant
````

and look for vnc in the list.    
    
    
3. After making a backup, replace your ~/.vnc/xstartup file with this:

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





