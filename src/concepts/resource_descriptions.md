---
title: Resource Descriptions
layout: default
---

<br><br>

# Resource Descriptions
<hr>

<br>

Resource descriptions help the managers of TeMoto to locate [resources](/temoto-telerobotics.github.io/site/p_conceptual_overview.html#resources). For example if you have a LIDAR you want to use, and the LIDAR has a ROS driver package (such as [urg_node](http://wiki.ros.org/urg_node)), then the resource description for this given device (more percisely - sensor description) contains information about the sensor type, package name, launch files/nodes and according input/output topics.

TeMoto has 4 different resource description types, described in YAML format. Each of them has to have according YAML file name:
* ***Sensor Descriptions*** - sensor_description.yaml
* ***Algorithm Descriptions*** - algorithm_description.yaml
* ***Tracker Descriptions*** - tracker_description.yaml
* ***Robot Descriptions*** - robot_description.yaml

Following subsections specify the YAML format for each resource description type.

## Sensor Descriptions

A sensor description file can contain multiple descriptions under *"Sensors:"* tag (see the examples below). This means that you could put all sensor descriptions into a single sensor description file or you could keep your system modular and add an appropriate sensor description to according ros package.

``` yaml
Sensors:                        # REQUIRED
- sensor_name: "name_1"         # REQUIRED
  sensor_type: "type_1"         # REQUIRED
  package_name: "package_1"     # REQUIRED
  executable:  "executable_1"   # REQUIRED - accepts launch file and node names
  output_topics:                # REQUIRED
    output_topic_type_1: "/name_of_the_ros_topic_1"
    output_topic_type_2: "/name_of_the_ros_topic_2"
    output_topic_type_n: "/name_of_the_ros_topic_n"

- sensor_name: "name_2"
  sensor_type: "type_2"
  package_name: "package_2"
  executable:  "executable_2"
  output_topics:           
    topic_type_1: "/name_of_the_ros_topic_1"
```

Example:

``` yaml
Sensors:
- sensor_name: "Logitech Camera"
  sensor_type: "camera"
  package_name: "usb_cam"
  executable:  "usb_cam-test.launch"
  output_topics:
    camera_info: "usb_cam/camera_info"
    camera_data: "usb_cam/image_raw"

- sensor_name: "Hokuyo LIDAR"
  sensor_type: "lidar"
  package_name: "urg_node"
  executable:  "urg_lidar.launch"
  output_topics:           
    lidar_status: "laser_status"
    lidar_data: "scan"
```

## Algorithm Descriptions

Algorithm descriptions are very similar to sensor descriptions. The difference is that in addition to ouput topics, algorithm descriptions also support input topics.

``` yaml
Algorithms:                                         # REQUIRED
- algorithm_name: "name"                            # REQUIRED
  algorithm_type: "type"                            # REQUIRED
  package_name: "package_name"                      # REQUIRED
  executable:  "executable"                         # REQUIRED
  input_topics:                                     # OPTIONAL
    input_topic_type_1: "/name_of_the_ros_topic_1"
    input_topic_type_2: "/name_of_the_ros_topic_2"
  output_topics:                                    # OPTIONAL
    output_topic_type_1: "/name_of_the_ros_topic_1"
    output_topic_type_2: "/name_of_the_ros_topic_2"
    output_topic_type_n: "/name_of_the_ros_topic_n"
```

Example:

``` yaml
Algorithms:
- algorithm_name: "AR tag detector"
  algorithm_type: "artag"
  package_name: "ar_track_alvar"
  executable:  "pr2_indiv_no_kinect.launch"
  input_topics:
    camera_info: "camera_info"
    camera_data: "usb_cam/raw"
  output_topics:
    visualization_data: "visualization_marker"
    marker_data: "ar_pose_marker"
```
