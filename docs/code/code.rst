========
Code
========
	
The main repository is https://github.com/stanfordroboticsclub/StanfordQuadruped. Check out the repository’s README for detailed instructions on how to install the requirements, set up the software, and calibrate and run the robot.

For reference, these additional repositories will be installed automatically when you set up StanfordQuadruped:
https://github.com/stanfordroboticsclub/PupperCommand
https://github.com/stanfordroboticsclub/PS4Joystick

How it works
-------------

.. image:: ../_static/diagram1.jpg
    :align: center
    :alt: overview diagram

The main program is **run_robot.py** which is located in this directory. The robot code is run as a loop, with a joystick interface, a controller, and a hardware interface orchestrating the behavior. 

The joystick interface is responsible for reading joystick inputs from a UDP socket and converting them into a generic robot **command** type. A separate program, **joystick.py**, publishes these UDP messages, and is responsible for reading inputs from the PS4 controller over bluetooth. The controller does the bulk of the work, switching between states (trot, walk, rest, etc) and generating servo position targets. A detailed model of the controller is shown below. The third component of the code, the hardware interface, converts the position targets from the controller into PWM duty cycles, which it then passes to a Python binding to **pigpiod**, which then generates PWM signals in software and sends these signals to the motors attached to the Raspberry Pi.
	
.. image:: ../_static/diagram2.jpg
    :align: center
    :alt: controller diagram

This diagram shows a breakdown of the robot controller. Inside, you can see four primary components: a gait scheduler (also called gait controller), a stance controller, a swing controller, and an inverse kinematics model. 

The gait scheduler is responsible for planning which feet should be on the ground (stance) and which should be moving forward to the next step (swing) at any given time. In a trot for example, the diagonal pairs of legs move in sync and take turns between stance and swing. As shown in the diagram, the gait scheduler can be thought of as a conductor for each leg, switching it between stance and swing as time progresses. 

The stance controller controls the feet on the ground, and is actually quite simple. It looks at the desired robot velocity, and then generates a body-relative target velocity for these stance feet that is in the opposite direction as the desired velocity. It also incorporates turning, in which case it rotates the feet relative to the body in the opposite direction as the desired body rotation. 

The swing controller picks up the feet that just finished their stance phase, and brings them to their next touchdown location. The touchdown locations are selected so that the foot moves the same distance forward in swing as it does backwards in stance. For example, if in stance phase the feet move backwards at -0.4m/s (to achieve a body velocity of +0.4m/s) and the stance phase is 0.5 seconds long, then we know the feet will have moved backwards -0.20m. The swing controller will then move the feet forwards 0.20m to put the foot back in its starting place. You can imagine that if the swing controller only put the leg forward 0.15m, then every step the foot would lag more and more behind the body by -0.05m. 

Both the stance and swing controllers generate target positions for the feet in cartesian coordinates relative the body center of mass. It's convenient to work in cartesian coordinates for the stance and swing planning, but we now need to convert them to motor angles. This is done by using an inverse kinematics model, which maps between cartesian body coordinates and motor angles. These motor angles, also called joint angles, are then populated into the **state** variable and returned by the model. 


Software Installation
-----------------------

Materials
^^^^^^^^^^

* Raspberry Pi 4
*  SD Card (32GB recommended)
*   Raspberry Pi 4 power supply (USB-C, 5V, >=3A)
*    Ethernet cable

Steps
^^^^^^

1. Set up the Raspberry Pi
############################

* Follow the instructions on `RPI-Setup`_ to prepare the Pi's SD card
*  Note: It's important to connect to the internet over wifi instead of over ethernet because we modify the ethernet port's settings to allow us to use the UDPComms library for inter- and intra-device communication. If you're having problems with the networking, specifically with UDPComms failing, check out this issue: https://groups.google.com/forum/#!topic/stanford-quadrupeds/GO5GPiBUcnc

2. Connect to the Pi over SSH 
##############################

Check that it has access to the internet. If you're having trouble SSH-ing into the Pi, please check the instructions for setting the Pi's ethernet settings linked in the previous step.


::

	ssh pi@10.0.0.Y
	
	* Here, "Y" is the IP address you chose for the Pi when running the install_packages.sh script. When prompted for the password, enter the default password "raspberry" or the one you set in the install_packages.sh script.

3. Test for the internet connection. 
######################################

It should only run 4 tests, if it continues, use Ctrl + C to stop it. Below is a piture of a successful run. 

:: 

	ping www.google.com
	
.. image:: ../_static/pingresults.JPG
    :align: center


If that doesn't work, do:

:: 
	
	ifconfig
	
and check the wlan0 portion to check if you have an IP address and other debugging info.


4. Clone this repo (on the Pi)
################################

::

	git clone https://github.com/stanfordroboticsclub/StanfordQuadruped.git

5. Install requirements (on the Pi)
#####################################

::

	cd StanfordQuadruped
	sudo bash install.sh

6. Power-cycle the robort
#############################

** Under Construction! **

RPI-Setup
-------------

Purpose
^^^^^^^^

This repository allows you to set up a Raspberry Pi solely by writing to the /boot partition (i.e.  the one you can write from most computers!) in a repeatable manner. This allows you to distribute a small .zip file to set up a Raspberry Pi to do anything.  You tell the user to unzip it over the top of the Pi's boot partition - the system can set itself up perfectly on the first boot, and once everything is ready to go reboot.
This is done using `pi-init2 <https://github.com/stanfordroboticsclub/RPI-Setup/blob/master/src/projects.bytemark.co.uk/pi-init2/init.go>`_ You can read more about how it works behind the scenes `here <https://blog.bytemark.co.uk/2016/01/04/setting-up-a-raspberry-pi-perfectly-on-the-first-boot>`_
Additionaly pi-init2 various system files are symlinked back to the /boot, allowing you to reliably edit those "user-serviceable" files from the computer in future. 
Another thing this repository will do is automated setting up the SD card in read-only mode as `described here <https://learn.adafruit.com/read-only-raspberry-pi>`_ . This is especially important for cases where the pi can get its power cut off without propper shutdown (for example in robotics) as it will prevent SD card corruption. 
This adds an **(ro)** (read-only) indicator to the bash prompt indicating that the file system can't be changed. 
To make changes you can use the **rw** and **ro** bash commands to transision between read-write and read-only modes respectivaly. 
A number of directories (including **/tmp**, **/var/log** and **/var/tmp**) are remapped to a `tmpfs <https://en.wikipedia.org/wiki/Tmpfs>`_ to ensure programs that expect them to be writable contiune to work.

Setting up the SD card for the PI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From your desktop / laptop:

1. Put the SD card into your desktop / laptop. 
###############################################

2. Download and write a standard Raspbian Buster Lite SD card.
#################################################################

Use `this version <https://slack-files.com/T0RAWRCGY-FQG7WTSBH-eb9549ed22>`_ so everyone is using the same version. Unzip and extract the file. 


3. We recomend using this `etcher <https://www.balena.io/etcher/>`_ to flash the card. 
##########################################################################################

You'll have to download it on your computer. 

* If you are using the recommended etcher, this is the start-up menu. Select 2019-09-26-raspbian-buster-lite.img (file inside zip )and the SD card. 

.. image:: ../_static/flash1.JPG
    :align: center

*  Image of SD card being flashed. 

.. image:: ../_static/flash2.JPG
    :align: center

*   Done!

.. image:: ../_static/flash3.JPG
    :align: center

4. The SD card should now be filled with files and renamed boot. 
###################################################################

Sometimes it takes some time for your computer to read the SD card and show the boot folder. Try removing the SD card and putting it back in, if the problem persists. 

.. image:: ../_static/sdboot.JPG
    :align: center

5. Download the latest release of this repository. 
#####################################################

Unzip and extract all the files. 

.. image:: ../_static/downloadrpi.jpg
    :align: center
	
6. Move all the files in the repository into the SD card. 
#############################################################

Replace any files that conflict so the repository's version overwrites the original version. You can now delete the zip file and the now empty folder.  


.. image:: ../_static/replaceboot.jpg
    :align: center

7. Remove SD card from computer and put it into your Raspberry Pi. 
####################################################################

Getting internet access at Stanford
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This script will make so the RPi automatically wants to connect the Stanford network. Initially it won't be able to do that as it is not yet authenticated to do it. To set that up:

* Plug your Pi in to power (over the onboard micro USB port). Either plug a monitor and keyboard into the Pi or SSH into it using your laptop over Ethernet. Log in to the Pi. In the welcome message that comes after the login line, look for the Pi's MAC address, which will appear under the line that says "wireless Hardware MAC address". Note that address down.
*  Use another computer to navigate to iprequest.stanford.edu.
*   Log in using your Stanford credentials.
*    Follow the on-screen instructions to add another device:

     * **First page:** Device Type: Other, Operating System: Linux, Hardware Address: put Pi's MAC address
     *  **Second page:** Make and model: Other PC, Hardware Addresses Wired: delete what's there, Hardware Addresses Wireless: put Pi's MAC address

*     Confirm that the Pi is connected to the network:

      * Wait for an email (to your Stanford email) that the device has been accepted
      *  **sudo reboot** on the Pi
      *   After it's done rebooting, type ping www.google.com and make sure you are receiving packets over the network


Getting internet access elsewhere
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are two methods for getting internet access elsewhere: using the raspi-config tool on the Pi or changing the wpa_supplicant file in the SD card file system. Using the raspi-config tool is simpler and recommended for beginners, but the benefits of modifying the wpa_supplicant file is that you can set the proper internet settings before starting up the Pi, which may help in scenarios where you'd like to do as little setup on the Pi as possible.

1. Raspi-config method

Once SSH'd into the Pi, run:

::

	sudo raspi-config

This is the menu that will appear. Go to Network Options, then Wi-Fi and enter your SSID (Wi-Fi name, eg. Netgear, Linksys) and password. 

.. image:: ../_static/raspconfig1.JPG
    :align: center

.. image:: ../_static/raspconfig2.JPG
    :align: center

2. Wpa_supplicant method

Edit **/etc/wpa_supplicant/wpa_supplicant.conf** as documented in `this link <https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md>`_ , see "Adding the network details to the Raspberry Pi". You can also see this `link <https://linux.die.net/man/5/wpa_supplicant.conf>`_. Thanks to pi-init2 magic that file can be edited before the pi is ever turned on from **/boot/appliance/etc/wpa_supplicant/wpa_supplicant.conf**

Getting started with the Pi
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Configure your computer to access the robot network: 

	* To use ethernet for set up (recommended), connect the ethernet cable to your computer and the raspberry pi. 
	*  Go to your network settings for the interface you wish to use (ethernet/wifi)
	*   Change your Configure IPv4: Manually
	*    Change your IP Address to something in range 10.0.0.X (If you are a part of Stanford Student Robotics pick something that doesn't colide with other systems from this `document <https://docs.google.com/spreadsheets/u/1/d/1pqduUwYa1_sWiObJDrvCCz4Al3pl588ytE4u-Dwa6Pw/edit?usp=sharing>`_ )
	*     Change your Subnet Mask: 255.255.255.0
	*      Leave the Router blank
	*       After disconnecting from the robot network remember to return those settings to what they orignially were, otherwise your internet on that interface won't work

*  Ssh into the pi from your computer. If it asks for a password,it is: raspberry  

::

	ssh pi@10.0.0.10


.. image:: ../_static/sshimage.jpg
    :align: center

*   Type rw to enter read-write mode. Confirm that the terminal prompt ends with (rw) instead of (ro)

.. image:: ../_static/readwrite.JPG
    :align: center

*    To install packages run:

::

	sudo ./install_packages.sh 

	* If the IP is still 10.0.0.10 you will be prompted to change it. The raspberry Pi IP should not conflict with your computer's IP, 10.0.0.Y. 
	*  If the hostname is still raspberry you will be prompted to change it.  
	*   You will be asked to enter the current time and date. This is needed so that certificates don't get marked as expired. There is a time_sync.sh script that updates the current time from google

What this repo does
^^^^^^^^^^^^^^^^^^^^

* Enables ssh. Because the password is kept unchanged (raspberry) ssh is only enabled on the ethernet interface. Comment out the ListenAddress lines from /boot/appliance/etc/ssh/sshd_config to enable it on all interfaces.
*  Sets the Pi to connect to the robot network (10.0.0.X) over ethernet
*   Expands the SD card file system
*    Sets the file system up as read-only
*     Prepares to connect to Stanford WiFi (see above for details)
*      Gives the script to install tools and repos needed for development

Building pi-init2
^^^^^^^^^^^^^^^^^^^

This repo inculdes the pi-init2 binary and there shouldn't be any reason to recompile it. If you need to there is a included Makefile


Calibration
------------

Calibration is a necessary step before running the robot because that we don't yet have a precise measurement of how the servos arms are fixed relative to the servo output shafts. Running the calibration script will help you determine this rotational offset by prompting you to align each of the 12 degrees of freedom with a known angle, such as the horizontal or the vertical.

Materials
^^^^^^^^^^

1. Finished robot
2. Some sort of stand to hold the robot up so that its legs can extend without touching the ground/table.


Steps 
^^^^^^

1. Plug in your 2S Lipo battery


Running the Robot 
-------------------

Robot Controls
----------------

Notes
------
