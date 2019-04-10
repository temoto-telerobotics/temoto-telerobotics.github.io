---
title: External Resource Manager
layout: default
---

<br><br>

# External Resource Manager
<hr>
[Back to Resource Access Layer](/temoto-telerobotics.githubub.io/site/layers/resource_access_layer) <br>
[Back to Architecture](/temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#architecture) <br>
<br>

Resource Manager. Its role is to initiate external resources, via ROS nodes and launch files, upon a request. The Resource Manager is primarily used by the Management layer for initiating sensors, data processing algorithms, robotic devices, and other resources that are not built in TeMoto 2.0. Resource Manager periodically monitors the status of initialized resources and notifies the upper layers about unexpected events. 

