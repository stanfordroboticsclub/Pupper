=================
Robot operation
=================

Running the robot
-----------------
#. Plug in your 2S Lipo battery. 
    
    * If you followed the instructions above, the code will automatically start running on boot.
    * If you want to turn this feature off, ssh into the robot, go into rw mode, and then do:: 
        
        sudo systemctl disable robot
        

#. Connect the PS4 controller to the Pi by putting it pairing mode.
    
    * To put it into pairing mode, hold the share button and circular Playstation button at the same time until it starts making quick double flashes. 
    * If it starts making slow single flashes, hold the Playstation button down until it stops blinking and try again.

#. Wait until the controller binds to the robot, at which point the controller should turn a dim green (or whatever color you chose in pupper/HardwareConfig.py for the deactivated color). 
#. Press L1 on the controller to "activate" the robot. The controller should turn bright green (or again, whatever you chose in HardwareConfig).
#. You're good to go! Check out the controls section below for operating instructions.

Robot controls
---------------

* L1: Press to toggle active mode and deactivate mode.
    
    * Note: the PS4 controller's front light will change colors to indicate if the robot is deactivated or activated.

* R1: Press to transition between Rest mode and Trot mode
* Left joystick

    * Forward/back: moves the robot forward/backwards when in Trot mode
    * Left/right: moves the robot left/right when in Trot mode
* Right joystick
    
    * Forward/back: pitches the robot forward and backward
    * Left/right: turns the robot left and right
* D-Pad

    * Forward/back: raises and lowers the body
    * Left/rights: rolls the body left/right
* "X" button: Press it three times to complete a full hop


Notes
-------

* PS4 controller pairing instructions (repeat of instructions above)
    
    * To put it into pairing mode, hold the share button and circular Playstation button at the same time until it starts making quick double flashes. 
    * If it starts making slow single flashes, hold the Playstation button down until it stops blinking and try again.

* Battery voltage
    
    * If you power the robot with anything higher than 8.4V (aka >2S) you'll almost certainly fry all your expensive servos!
    * Also note that you should attach a lipo battery alarm to your battery when running the robot so that you are know when the battery is depleted. Discharging your battery too much runs the risk of starting a fire, especially if you try to charge it again after it's been completely discharged. A good rule-of-thumb for know when a lipo is discharged is checking whether the individual cell voltages are below 3.6V.

* Feet!

    * Using the bare carbon fiber as feet works well for grippy surfaces, including carpet. If you want to use the robot on a more slippery surface, we recommend buying rubber grommets (McMaster #90131A101) and fastening them to the pre-drilled holes in the feet. 