Utilizing the Managers
======================

.. note:: This tutorial assumes that you are familiar with the concept of `Actions <../../../temoto-telerobotics.github.io/site/concepts/actions>`_ and `Managers <../../../temoto-telerobotics.github.io/site/p_conceptual_overview.html#managers>`_ in TeMoto.

Actions can utilize the functionality of any particular manager through its interface. The aim of this tutorial is to show what are the common procedures for setting up and using manager interfaces. As a concrete example, this tutorial shows how to start a camera via `Sensor Manager <../../../temoto-telerobotics.github.io/site/managers/sensor_manager>`_ interface.

Hence, due the example we are trying to go for, it is required that you have a ROS package that is able to open stream the camera feed. The `"usb_cam" <http://wiki.ros.org/usb_cam>`_ package will work just fine. And since TeMoto and its ideas are not de facto standards in the ROS community, you have to add a `sensor descriptor <../../../temoto-telerobotics.github.io/site/concepts/resource_descriptions>`_ to the "usb_cam" package, which tells what kind of cameras are available and what kind of topics they output.

1) Create/Open the action source file 
"""""""""""""""""""""""""""""""""""""

For this particular tutorial we are going to create an action that is verbally invoked by sentence: *"Start the camera"*. Follow the `"Writing an Action" </temoto-telerobotics.github.io/tutorials/writing_an_action.html>`_ tutorial if you are not familiar with the process of creating actions.

Once you have created or located your action, open up the action source file under *"src"* directory of your action package. 

2) Include the interface header file
""""""""""""""""""""""""""""""""""""

In this case include the sensor manager interface header.

.. code-block:: c++

  #include "sensor_manager/sensor_manager_interface.h"

3) Add the manager interface object
"""""""""""""""""""""""""""""""""""

Add the sensor manager interface object as a private member of your TeMoto action class:

.. note:: Do not forget to add the name of your action as a template parameter: ``SensorManagerInterface<TaYourActionsName>``

.. code-block:: c++

  private:
  
    // Create sensor manager interface object for accessing sensor manager
    sensor_manager::SensorManagerInterface<TaStartCamera> smi_;

4) Initialize the interface
""""""""""""""""""""""""""""""

Initialize the interface in your ``void startInterface_x()`` method where "x" denotes the particular Action interface (remember, actions also have interfaces that are unrelated to manager interfaces).

.. code-block:: c++

  // Initialize the sensor manager interface
  smi_.initialize(this);

5) Use the interface
""""""""""""""""""""

This step depends on the particular behaviour you are trying to achieve, hence the following is just an example for acquiring camera resources via sensor manager interface.

.. code-block:: c++

  // A structure for containing sensor topic related information
  SensorTopicsReq requested_topics;

  // Specify what kind of topics the sensor should output
  requested_topics.addOutputTopicType("camera_data");
  requested_topics.addOutputTopicType("camera_info");

  // Debug information
  TEMOTO_INFO(" Starting the camera");

  /*
   * Invoke the "startSensor" method. This method requires the type of the sensor (camera)
   * and will output a structure that maintains the topic names which were received based 
   * on the request
   */
  SensorTopicsRes responded_topics = smi_.startSensor("camera", requested_topics);

  // Get the name of the topic where camera feed is published
  std::string camera_topic = responded_topics.getOutputTopic("camera_data");

  // Debug information
  TEMOTO_INFO_STREAM("Got camera on topic '" << camera_topic << "'");


6) Compile and test the action
""""""""""""""""""""""""""""""

* Compile the action.
* Test the action by typing *"Start the camera"* into the temoto termial. Debug informantion is printed out in the terminal where the *"temoto_agent"* was launched:

