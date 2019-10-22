Installing TeMoto
=================

TeMoto is comprised of different subsystems which, if needed, can be installed separately, e.g., if you only need 
`temoto_action_engine <https://github.com/temoto-telerobotics/temoto_action_engine>`_ then you can set it up individually.
If you want to get the whole TeMoto framework, then follow the instructions below:

**1) Set up ROS and create a catkin workspace:**

You can skip this part if you already have ROS and catkin workspace set up.

- `Setting up ROS <http://wiki.ros.org/ROS/Installation>`_
- `Creating a catkin workspace <http://wiki.ros.org/catkin/Tutorials/create_a_workspace>`_

**2) git clone temoto repository:**

.. code-block:: shell

    cd your_catkin_ws_dir/src
    git clone --recursive https://github.com/temoto-telerobotics/temoto.git

**3) Build your catkin workspace:**

.. code-block:: shell

    cd your_catkin_ws_dir
    catkin build  
