Installing TeMoto
=================
|
**1) Set up ROS and create a catkin workspace:**

You can skip this part if you already have ROS and catkin workspace set up.

- `Setting up ROS <http://wiki.ros.org/ROS/Installation>`_
- `Creating a catkin workspace <http://wiki.ros.org/catkin/Tutorials/create_a_workspace>`_

**2) git clone temoto repository:**

.. code-block:: shell

    cd your_catkin_ws_dir/src
    mkdir temoto && cd temoto
    git clone https://github.com/temoto-telerobotics/temoto.git

**3) Install dependencies:**

.. code-block:: shell

    bash temoto/scripts/temoto_setup.sh temoto

**4) Build your catkin workspace:**

.. code-block:: shell

    cd your_catkin_ws_dir
    catkin build  