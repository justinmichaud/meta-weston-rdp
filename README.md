This README file contains information on the content and usage of the meta-weston-rdp layer.

Note: This layer is intended for use with the MaaXBoard 8ULP Yocto project however it can serve as a starting point to adapt to other platforms.

# Table of Contents
1) [Adding the meta-weston-rdp layer to your build](#AddLayer)
2) [Launching FreeRDP](#Launch)


<div id='AddLayer'/>

## 1. Adding the meta-weston-rdp layer to your build

1) In the main bitbake recipe, for example avnet-image-oob.bb:
Add "freerdp \" to section "CORE_IMAGE_EXTRA_INSTALL:append"
```
CORE_IMAGE_EXTRA_INSTALL:append = " \    
...
    freerdp \
"
```
2) Copy or clone the "meta-weston-rdp" repo folder to the sources directory

3) Run the bitbake command to add the "meta-weston-rdp" layer 
```
$ bitbake-layers add-layer sources/meta-weston-rdp
```

4) Build the image
```
$ bitbake avnet-image-oob
```

5) Once the image is built and downloaded to the board, connect to the board and edit the weston.ini file.
The file is located here /etc/xdg/weston/weston.ini
Add "modules=screen-share.so" to [core] and modify the [screen-share] section as shown bellow and save
```
[core]
...
modules=screen-share.so

[screen-share]
command=/usr/bin/weston --backend=rdp-backend.so --rdp-tls-cert=/etc/freerdp/keys/server.crt --rdp-tls-key=/etc/freerdp/keys/server.key --shell=fullscreen-shell.so --no-clients-resize
start-on-startup=true
```
6) Generate server keys by executing the following commands on the target
The hostname in this case is maaxboard8ulp, modify this as needed for the target
```
$ mkdir /etc/freerdp
$ mkdir /etc/freerdp/keys
$ winpr-makecert -path /etc/freerdp/keys
$ mv /etc/freerdp/keys/maaxboard8ulp.crt /etc/freerdp/keys/server.crt
$ mv /etc/freerdp/keys/maaxboard8ulp.key /etc/freerdp/keys/server.key
```
7) reboot the target

<div id='Launch'/>

## 2. Launching FreeRDP

From another computer in the same network connect to the embedded
device using a Remote Desktop Protocol (RDP) client:

On the host system, run the following command to connect to the target, the hostname in this case is maaxboard8ulp, modify this as needed for the target
```
$ xfreerdp /u:root /v:maaxboard8ulp.local
```
or
```
$ xfreerdp /u:root /v:<IPV4 Address>
```


The FreeRDP service can be manually started using the following command
```
$ weston --backend=rdp-backend.so --rdp-tls-cert=/etc/freerdp/keys/server.crt --rdp-tls-key=/etc/freerdp/keys/server.key
```

If FreeRDP fails to connect try restarting Weston using this command
```
$ systemctl restart weston
```
