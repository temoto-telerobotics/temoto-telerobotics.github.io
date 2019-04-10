---
title: About TeMoto
layout: default
---

<br><br>

# About TeMoto
<hr>

<br>

## What is it? ##
TeMoto is a telerobotics application development framework that is built upon ROS. TeMoto helps to integrate and manage different user-defined sequences of code (*actions*) and hardware *resources* like actuators, robots and sensors. For more detailed overview, see the [Conceptual Overview](/temoto-telerobotics.githubub.io/site/p_conceptual_overview) section.

## Why would I need it? ##
A teleoperation process may comprise many different subtasks. Ultimatlely the application developer solves the tasks by introducing one or a bunch of computer programs. The architecture of the composed software depends on the requirements of the problem at hand, ranging from simple to sophisticated in terms of:
* **Reusability** - Should/Can the developed software be reused for other applications?
* **Extendability** - How easy it is to add new features to the software?
* **Scalability** - Can the software be extended to multiple robots that work towards the same goal?
* **Redundancy** - How easy it is to interchange similar components in the system?

TeMoto is a framework that helps to create and manage applications that tend to be sophisticated in the above mentioned terms. So if you are developing a telerobotics application and the code starts to look like a bowl of sphagetti, then you may consider using TeMoto.

## TeMoto applicatons vs traditional ROS applications ##

Applications developed in ROS are traditionally managed solely by the user. An application in ROS can be a simple executable/node or a complex combination of different nodes that work together (Fig. 1). Typically the latter is the case and in order to start the application, the user would need to either run each node manually (rosrun) or use launch files (roslaunch). After doing so, the node graph (configuration of running nodes) essentially remains static.

<p align="center" >
  <img width="500" src="doc/images/ros.png">
  <br> 
  <b> Fig. 1: </b> Conceptual layout of a "traditional" ROS application 
</p>

TeMoto's framework introduces a way to organize user-code into chunks called *actions* (see Fig. 2). An *action* can run arbitrary user-written code and can also request/control *resources* (including nodes) via *managers* (Explained further in the [conceptual overview](/temoto-telerobotics.githubub.io/site/p_conceptual_overview) section). The *actions* are invoked dynamically via natural language and hence the *resources* are started or stopped dynamically during run-time. Essentially TeMoto allows the user to programatically manage nodes during run-time. That's just one feature of the TeMoto's framework but in principle, this is how "traditional" and TeMoto applications differ.

<p align="center" >
  <img width="500" src="doc/images/temoto.png">
  <br>
  <b> Fig. 2: </b> Conceptual layout of an application in TeMoto. 
</p>
