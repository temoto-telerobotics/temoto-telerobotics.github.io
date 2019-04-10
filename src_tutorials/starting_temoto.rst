Starting Temoto
===============
|
TeMoto comprises `8 ROS nodes </temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#layers>`_ and depending on the application, all of them are not required to be running at all times. Yet it is advisable (and convenient) to launch the whole system, since the nature of TeMoto is dynamic and problems will start to occur when a non-running manager is required by the system. 

**1) Open a new terminal and launch the managers**

.. code-block:: shell

    roslaunch temoto_2 dual_temoto.launch

**2) Open another terminal and launch the** `Agent </temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#supervisory-layer>`_

.. code-block:: shell

    roslaunch temoto_2 temoto_agent.launch

After launching the Agent, a default action is executed which brings up the TeMoto Terminal. You can send verbal instructions to the Agent through the TeMoto Terminal.

**3) Play around with the "TeMoto Terminal"**

For example, type: ``add one inch and 43 inches``

.. note:: TeMoto has a marginal amount of built-in example actions, so "Unknown instruction" is a common and expected response from the Agent.
