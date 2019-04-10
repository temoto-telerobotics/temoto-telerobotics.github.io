---
title: Context Manager
layout: default
---

<br><br>

# Context Manager
<hr>
[Back to Management Layer](/temoto-telerobotics.githubub.io/site/layers/management_layer)<br>
[Back to Architecture](/temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#architecture)<br>
<br>

**The Context Manager** is responsible of perceiving the environment through a combination of sensors and data processing algorithms. For example, if the Robot Manager requires a 2D map of the environment in order to navigate inside it, the Context Manager will first request an appropriate sensor from the Sensor Manager and will create a map via mapping algorithm. The ROS topic for this map is then sent back to the Robot Manager which can now access the map. 
