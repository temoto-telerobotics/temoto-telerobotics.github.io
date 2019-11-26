Creating TeMoto Workspace
=========================
|
TeMoto workspace is a **ROS package** that conveniently bundles up different configuration and launch files that are necessary for **your TeMoto setup**.
In order to create your custom temoto workspace, follow these steps:

.. note:: Despite the7

**1) Create a TeMoto workspace:**

.. code-block:: shell

    rosrun temoto_workspace_generator generate_workspace NAME_FOR_YOUR_TEMOTO_WS ./your/desired/path

For example if you want to create the TeMoto workspace called `my_temoto_ws` to your current directory then:

.. code-block:: shell

    rosrun temoto_workspace_generator generate_workspace my_temoto_ws .

.. note:: Don't forget to add the dot

**2) Build your catkin workspace:**

.. code-block:: shell

    cd your_catkin_ws_dir
    catkin build  

.. note:: Don't forget to source your catkin workspace
