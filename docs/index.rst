Welcome to Pupper's documentation!
##################################

About Pupper
*************

Stanford Pupper is a small quadruped robot that can hop, trot, and 
run around. We hope that its low cost and simple design will allow 
robot enthusiasts in K-12 and beyond to get their hands on fun, dynamic robots.

.. raw:: html

    <div style="position: relative; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe width="560" height="315" src="https://www.youtube.com/embed/NIjodHA78UE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


The robot's brain is a Raspberry Pi 4 computer, which receives commands from a wireless PS4 controller and controls the servo motors, three per leg,
to move the feet and body to the right places. 

The robot is designed to be "hacked" -- we want you to be able to adjust and expand the robot's behaviors to your heart's content. 
While the robot can walk out-of-the-box, some of the features you could add include different gaits (bounding, galloping, etc), or
high level behaviors like playing fetch or following you around. You can also simulate the robot's motion in PyBullet
before touching the real robot.

To get started, check out the pages linked below on part sourcing and assembly. If you purchase the parts yourself, it'll run you 
about $900-$1000. However, you can purchase a kit to build the robot from either `MangDang <http://www.mangdang.net/Product?_l=en>`_ or 
`Cypress Software <https://cypress-software-inc.myshopify.com/>`_ for cheaper than what it would cost you to get the parts yourself. The two vendors sell 
different options so check both of them out to see what works for you. While we're not affiliated with either company, we've verified both of their kits.

.. toctree::
    :maxdepth: 1
    :caption: Build Guide

    guide/purchasing
    guide/assembly
    guide/software_installation
    guide/calibration
    guide/operation


.. toctree::
    :maxdepth: 1
    :caption: References

    reference/design
    reference/controller
    reference/help

.. image:: _static/pupperpic.PNG
    :align: center
    :alt: Pupper Jumping