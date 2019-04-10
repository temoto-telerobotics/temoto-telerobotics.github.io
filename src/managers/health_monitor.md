---
title: Health Monitor
layout: default
---

<br><br>

# Health Monitor
<hr>
[Back to Supervisory Layer](/temoto-telerobotics.githubub.io/site/layers/supervisory_layer) <br>
[Back to Architecture](/temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#architecture) <br>
<br>

**The Health Monitor** is a subsystem that gathers information about the use of resources and the state of other subsystems. When a subsystem acquires a resource or encounters an error, a timestamped report message is sent to the Health Monitor. It tries to find any patterns that have led a given subsystem to a significant erroneous behaviour and proposes a fix for keeping that error from recurring. For example, if a resource named camera_1x has frequently caused issues after initialization, the Health Monitor lowers its reliability score or prohibits its use completely.

