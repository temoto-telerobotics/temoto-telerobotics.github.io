Creating TeMoto Workspace
=========================
|
TeMoto workspace is a ROS package that conveniently bundles up different configuration and launch files that are necessary for your TeMoto setup.
In order to create your custom temoto workspace, follow these steps:

**1) Navigate to your temoto base folder:**

.. code-block:: shell

   cd your_catkin_ws_dir/src/temoto

**2) Create a temoto workspace:**

.. code-block:: shell

   rosrun temoto temoto_ws_generator NAME_FOR_YOUR_TEMOTO_WS   
