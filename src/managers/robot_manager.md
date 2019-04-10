---
title: Robot Manager
layout: default
---

<br><br>

# Robot Manager
<hr>
[Back to Management Layer](/temoto-telerobotics.githubub.io/site/layers/management_layer) <br>
[Back to Architecture](/temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#architecture) <br>
<br>

**The Robot Manager** maintains knowledge about robotic devices including manipulators, grippers, and mobile bases. A single device or an assembly (physical or virtual) of the aforementioned devices compose a robot which is viewed as a resource. The robot can be controlled after an instance of robot resource is created. In addition to controlling the EEF and the base of the robot, the Robot Manager introduces the concept of a default EEF target which means that the EEFs target pose can be locked to a set target. A target could be anything that has identifiable pose, such as a tracked hand or an object.

