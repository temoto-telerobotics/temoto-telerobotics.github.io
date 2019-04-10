---
title: Output Manager
layout: default
---

<br><br>

# Output Manager
<hr>
[Back to Management Layer](/temoto-telerobotics.github.io/site/layers/management_layer) <br>
[Back to Architecture](/temoto-telerobotics.github.io/site/p_conceptual_overview.html#architecture) <br>
<br>

**The Output Manager** provides the functionality to output audio-visual data. Similarly to the Sensor Manager, the Output Manager maintains descriptions about different types of output methods and how to access them. ROS RViz[TODO: ref] is used as the main platform for visualization. Each displayable data type, that is supported by the Output Manager, is displayed via dynamically loadable RViz plugins (treated as a resource in the Output Manager). The audio part of the manager allows synthesizing speech from text and play back audio files.


