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
* "X" button: Press it three times to complete a full hop
* Right joystick
    
    * Forward/back: pitches the robot forward and backward
    * Left/right: turns the robot left and right

* Left joystick

    * Forward/back: moves the robot forward/backwards when in Trot mode
    * Left/right: moves the robot left/right when in Trot mode