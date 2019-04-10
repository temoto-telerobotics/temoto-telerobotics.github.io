---
title: Sensor Manager
layout: default
---

<br><br>

# Sensor Manager
<hr>
[Back to Management Layer](/temoto-telerobotics.github.io/site/layers/management_layer) <br>
[Back to Architecture](/temoto-telerobotics.github.io/site/p_conceptual_overview.html#architecture) <br>
<br>

**The Sensor Manager** maintains knowledge (sensor descriptions) about different types of sensors and directs the data flow of a requested device. A sensor description contains information about the type of the sensor, how to access this resource (name of the sensor’s ROS package and according executable) and which ROS topic to use to access the output data. Sensor descriptions are initially stored in the respective ROS sensor packages, which will be scanned when the Sensor Manager is initialized. A local copy of found descriptions is used for serving incoming sensor requests.
