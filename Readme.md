# RaspiWiFi

RaspiWiFi is a program to headlessly configure a Raspberry Pi's WiFi connection using using any other WiFi-enabled device (much like the way
a Chromecast or similar device can be configured). It is relatively easy to start with the instruction below, **but you are strongly recommended to carefully review the "Trouble shooting" part just in case.** 
 
It can also be used as a method to connect wirelessly point-to-point with your Pi when a network is not available or you do not want to connect to one. Just leave it in Configuration Mode, connect to the "RaspiWiFi[xxxx] Setup" access point. The Pi will be addressable at 10.0.0.1 **(static IP address)** using all the normal methods you might use while connected through a network.

RaspiWiFi has been tested with the Raspberry Pi B+, Raspberry Pi 3, and Raspberry Pi Zero W.

### OS IMAGE USAGE:

== Just burn the ".IMG" file attached to this release to an 8GB+ SD card. Boot your Raspberry Pi with the SD card and it will automatically boot into its AP Host (broadcast) mode with an SSID based on a unique id (the last four of your Pi's serial number). No input devices or displays necessary. Otherwise this is a base install of the current Raspbian Stretch, up to date as of the date of
this release.

### SCRIPT-BASED INSTALLATION INSTRUCTIONS:

== Navigate to the directory where you downloaded or cloned RaspiWiFi

== Run:

sudo python3 initial_setup.py

== This script will install all necessary prerequisites and copy all necessary config and library files, then reboot. When it finishes booting it should
present itself in "Configuration Mode" as a WiFi access point with the name "RaspiWiFi[xxxx] Setup".

== The original RaspiWiFi directory that you ran the Initial Setup is no longer needed after installation and can be safely deleted. All necessary files are copied to /usr/lib/raspiwifi/ on setup.


### CONFIGURATION:

== You will be prompted to set a few variables during the Initial Setup script:

==== "SSID Prefix" [default: "RaspiWiFi Setup"]: This is the prefix of the SSID that your Pi will broadcast for you to connect to during Configuration Mode (Host Mode). The last four of you Pi's serial number will be appended to whatever you enter here.

==== "WPA Encryption" [default: No]: If oyu enable this setting the Access Point created during Configuration Mode will be encrypted using WPA2 encryption. The prompt following this one will let you specify the Wireless Key to be used. You can leave the password blank if you chose 'N' to this option. 

==== "Auto-Config mode" [default: n]: If you choose to enable this mode your Piwill check for an active connection while in normal operation mode (Client Mode). If an active connection has been determined to be lost, the Pi will reboot back into Configuration Mode (Host Mode) automatically.

==== "Auto-Config delay" [default: 300 seconds]: This is the time in consecutiveseconds to wait with an inactive connection before triggering a reset into Configuration Mode (Host Mode). This is only applicable if the "Auto-Config mode" mentioned above is set to active.

==== "Server port" [default: 80]: This is the server port that the web server hosting the Configuration App page will be listening on. If you change
this port make sure to add it to the end of the address when you're connecting to it. For example, if you speficiy 12345 as the port number
you would navigate to the page like this: http://10.0.0.1:12345 If you leave the port at the default setting [80] there is no need to specify the
port when navigating to the page.

==== "SSL Mode" [default: n]: With this option enabled your RaspiWifi configuration page will be sent over an SSL encrypted connection (don't forget the "s" when navigating to https://10.0.0.1:9191 when using this mode). You will get a certificate error from your web browser when connecting. The error is just a warning that the certificate has not been verified by a third party but everything will be properly encrypted anyway.

== All of these variables can be set at any time after the Initial Setup has been running by editing the /etc/raspiwifi/raspiwifi.conf


### USAGE:

== Connect to the "RaspiWiFi[xxxx] Setup" access point using any other WiFi enabled device.

== Navigate to [10.0.0.1], [raspiwifisetup.com], or [idliketoconfigurethewifionthisdevicenowplease.com] (I was debating whether this was funny or not and, yes, it was) using any web browser on the device you connected with. (don't forget to manually start with [https://] when using SSL mode)

== Select the WiFi connection you'd like your Raspberry Pi to connect to from the drop down list and enter its wireless password on the page provided. If no encryption is enabled, leave the password box blank. You may also manually specify your network information by clicking on the "manual SSID entry ->" link.

== Click the "Connect" button.

== At this point your Raspberry Pi will reboot and connect to the access point specified.

== You can view the current WPA encryption settings and change them from the main Web Configuration interface. The current settings are visible in a panel in the upper left corner of the screen. If you click the values in this display you will be taken to a page where you can change them. If you change them your device will reboot to  enable the new configuration. 

== You can also use the Pi in a point-to-point connection mode by leaving it in Configuration Mode. All services will be addresible in their normal way at 10.0.0.1 while connected to the "RaspiWiFi[xxxx] Setup" AP.



### RESETTING THE DEVICE:

== If GPIO 18 is pulled HIGH for 10 seconds or more the Raspberry Pi will reset all settings, reboot, and enter "Configuration Mode" again. It's useful to have a simple button wired on GPIO 18 to reset easily if moving to a new location, or if incorrect connection information is ever entered. Just press and hold for 10 seconds or longer.

== You can also reset the device by running the manual_reset.py in the /usr/lib/raspiwifi/reset_device directory as root or with sudo.


### UNINSTALLATION:

== You can uninstall RaspiWiFi at any time by running:
   
   **sudo python3 /usr/lib/raspiwifi/uninstall.python3**

   You can also run it from the "libs/" directory from a fresh clone if you've installed from a previous version and don't have /usr/lib/raspiwifi/uninstall.py available.
   
   
## Trouble shooting
Here are some possible issues you may have:
1. **Sometimes even after you run "initial_setup.py" and have configured the wifi, the Pi just stays at "Host mode":** <br>
   == The host mode related files are not removed correctly, probably because unknown bugs in "app.py" file. Check the following files to fix:<br>
      (1) "/etc/raspiwifi/host_mode", if this is removed or not.<br>
      (2) "/etc/cron.raspiwifi/", go to this directory, check if "aphost_bootstrapper" is deleted or not.<br>
      (3) "/etc/dhcpcd.conf", look at this file, make sure the "static IP" for wlan0 has been deleted.<br>
2. **Sometimes after you run "uninstall.py" and reboot the Pi, it losts the wifi connection, says "can't communicate with wpa_supplicant.conf file":**<br>
   == Run "uninstall.py" again (maybe more times needed) and manually check if "/etc/wpa_supplicant/wpa_supplicant.conf" is correctly modified.<br> 
3. **How to easily see the webpage without going through the whole process?**<br> 
   == Run "uninstall.py" first.<br> 
   == Then "cd /etc" -> "sudo mkdir raspiwifi" -> "cd raspiwifi/" -> "touch raspiwifi.conf" (Generate this file first)<br> 
   == Run "sudo cp /home/pi/RaspiWiFi/libs/reset_device/static_files/raspiwifi.conf /etc/raspiwifi/raspiwifi.conf". Then, run "app.py" under the               corresponding directory and open the browser to see it.<br> 
4. **For Other IP or modes related bugs:**<br>
   == Check "/etc/dhcpcd.conf", see if it is corresponding to the current mode. The original file should only be: "interface wlan0", no static IP.<br>
5. **Make sure each time after you commit the code to Github, run "uninstall.py" then "initial_setup.py" to update the changes.**<br>
6. ### All the "install" & "uninstall" process MUST be done under the original folder "RaspiWifi". Even though you use absolute path, you will still get severe bugs. (Refer to line 17 in "setup_lib.py")<br>
7. When running **"initial_setup.py"**, **MAKE SURE** the following files exist **FIRST** to successfully continue the initail setup process!!<br>
   == /etc/wpa../wpa... => wpa.original<br> 
   == /etc/dnsmasq.conf => dnsmasq.conf.original<br>
   == cp ..from repo/dnsmasq.conf => /etc/dnsmasq.conf<br>
   == /etc/dhcpcd.conf => dhcpcd.conf.original<br> 
   == if wpa_enable_choice == N:<br>
      ...from repo/hostpad.conf.nowpa => /etc/hostapd/hostapd.conf<br>
   If all of the files above are correctly generated, then the corresponding process "uninstall" should be good to go through.
  


