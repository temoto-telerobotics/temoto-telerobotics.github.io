Using The Component Manager
===========================

This tutorial covers the basic usage of the Component Manager -- a manager which maintains knowledge about different components,
such as cameras, LIDARs, data processing algorithms, etc. Actions can utilize the functionality of any particular Manager through
the interface of an according Manager and the Component Manager follows the same pattern. 

**This tutorial shows how to:**

* create a custom *components.yaml* and *pipes.yaml* file
* set up the Component Manager Interface in a TeMoto Action
* start a component and a pipe via Component Manager Interface
* set up a custom component and pipe failure recovery routine

Component Manager Overview
--------------------------

Figure 1. shows the conceptual overview of how Component Manager works and how it's used. The **Component Manager** itself is a ROS
node which can be accessed through **Component Manager Interface** (a C++ header that implements necessary ROS service clients
and exposes simplied interface to the user).

**The components** themselves are regarded as something that produce data (e.g., sensor)
or process data (e.g, algorithm). Each component is a ROS node, and the Component Manager only maintains semantic knowledge about
them and invokes them upon a request -- so **components are not idling** in the background, they are executed dynamically during
run-time.

The semantic knowledge about the components (ROS package name; name of the executable; output topics; etc) is maintained in a
**components.yaml** file and the Component Manager is periodically going thorugh the *catkin_workspace/src* directory and looks
recursively for *components.yaml* files (which means that you can have multiple *components.yaml* files, one per each component
package if you like).

.. figure:: /images/component_manager/component_manager_concept.png
    :width: 450px
    :align: center

    **Figure. 1:** Conceptual overview of how Component Manager works and how it's used. 

**Pipe management** is the second main feature of the Component Manager. Pipes are composed of **segments**, i.e., components, that sequentially pass 
data from one segment to another. For example Figure 2. depicts a pipe which tracks objects based on filtered video data. The pipes are described
via *pipes.yaml*, which, similarly to **components.yaml** are periodically indexed by the Component Manager.

.. figure:: /images/component_manager/component_manager_pipe_example.png
    :width: 400px
    :align: center

    **Figure. 2:** Example of a component pipe which tracks objects based on filtered video data. 


Describing and using Components
-------------------------------

As a concrete example, we are going to start a camera and write a custom failure behaviour. Hence, due to the example we are trying
to go for, it is required that you have a ROS package that is able to open stream the camera feed. The `"usb_cam" <http://wiki.ros.org/usb_cam>`_ 
package will work just fine.

1) Create a *components.yaml* file 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can create this file where ever in your catkin workspace but for the sake of having a clean workspace, create it in your
TeMoto Workspace root directory.

.. note:: Follow the *"Creating TeMoto workspace*" tutorial if you haven't created a TeMoto workspace yet.

If you aready have the *components.yaml* file then great, you can just append it.

**So in your *components.yaml* file, outline what's the:**

* **name of the component**
* **type of the component**
* **name of the package** where this component originates
* **name of the executable** (something that you *rosrun/roslaunch*)
* **input topics** types and default values (if any)
* **output topics** types and default values (if any)
* **parameters** and default values (if any)

.. note:: The *component name*, it's *type* and the *topic types* are arbitrary, there are no hardcoded/preferred values in the Component Manager. The key is to use the chosen names consistently

For example, this is how the *component.yaml* file looks like for the `"usb_cam" <http://wiki.ros.org/usb_cam>`_ based camera component:

.. code-block:: yaml

  Components:
  - component_name: "My Laptop Integrated 2D Camera"
    component_type: "2d_camera"
    package_name: "usb_cam"
    executable:  "usb_cam-test.launch"
    output_topics:
      camera_data: "usb_cam/image_raw"
      camera_info: "usb_cam/camera_info"

This is just a simple example that gets the usb_cam recognized by the Context Manager. Unfortunately the default launch file that's provided
by the usb_cam package does not include argument remapping functionalities (important if you want your component to publish on a custom topic).
You can use `this launch file instead <https://github.com/temoto-telerobotics-demos/icra_2020_ws/blob/jackal-ws/src/jackal_temoto_ws/jackal_temoto_ws/launch/usb_cam_remappable.launch>`_
if you want to enable remapping for your usb_cam based camera component.

2) Set up the Component Manager Interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Next we are going to modify the *.cpp* file of your Action and make it invoke the camera through Component Manager Interface.

.. note:: Follow the *"Writing an Action*" tutorial if you haven't created a TeMoto Action yet.
.. note:: Pay close attention to whether your Action should be synchronous or asynchronous.

But first add Component Manager as a dependency in the *package.xml* and *CMakeLists.txt* files:

.. code-block:: xml

  <build_depend>temoto_component_manager</build_depend>
  <exec_depend>temoto_component_manager</exec_depend>

.. code-block:: make

  find_package(catkin REQUIRED COMPONENTS
    ...
    temoto_component_manager
  )

**Open up the *<your_action_name>.cpp* file in the src directory of your Action and add the following lines:**

Include the Component Manager Interface header.

.. code-block:: c++

  #include "temoto_component_manager/component_manager_interface.h"

Add the Component Manager Interface object as a member of your TeMoto Action class:

.. code-block:: c++

  public:
  
    // Create component manager interface object to access the component manager
    temoto_component_manager::ComponentManagerInterface<TaYourActionsName> cmi_;

.. note:: Do not forget to add the name of your action as a template parameter: ``ComponentManagerInterface<TaYourActionsName>``

Initialize the interface in the ``void executeTemotoAction()`` method.

.. code-block:: c++

  // Initialize the sensor manager interface
  cmi_.initialize(this);

3) Start the component
~~~~~~~~~~~~~~~~~~~~

This step depends on the particular behaviour you are trying to achieve, hence the following is just an example for 
acquiring camera component via Component Manager Interface. 

.. code-block:: c++

  void executeTemotoAction()
  {
    // Initialize the component manager interface
    cmi_.initialize(this);

    // A structure for containing component topic related information
    ComponentTopicsReq requested_topics;

    // Specify what kind of topics the component should output. These are the same as
    // outlined in the components.yaml file
    requested_topics.addOutputTopicType("camera_data");

    // Debug information
    TEMOTO_INFO("Starting the 2d_camera component ...");

    /*
     * Invoke the "startComponent" method. This method requires the type of the sensor (2d_camera)
     * and will output a structure that maintains the topic names which were received based 
     * on the request
     */
    ComponentTopicsRes responded_topics = cmi_.startComponent("2d_camera", requested_topics);

    // Get the name of the topic where camera feed is published
    std::string camera_data_topic = responded_topics.getOutputTopic("camera_data");

    // Debug information
    TEMOTO_INFO_STREAM("Got camera data on topic '" << camera_topic << "'");
  }

4) Add a custom recovery routine
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the component should fail in any way that leads to it's termination, the Component Manager will automatically send a
message to Component Manager Interface, indicating the failure. By default the Component Manager Interface will load
a component with similar parameters (component type, topic types, etc.) again but you can define your own custom routine
that gets invoked instead. 

For that create a method with a return type of ``void`` and which accepts ``const temoto_component_manager::LoadComponent&`` reference:

.. code-block:: c++

  /**
   * @brief A custom component failure recovery routine. You can implement whatever you like
   * in here, such as logging, system rollbacking, starting an alternative component ... 
   */
  void componentStatusCb(const temoto_component_manager::LoadComponent& comp_srv_msg)
  {
    TEMOTO_WARN_STREAM("Received a status message:\n" << comp_srv_msg.request);
    TEMOTO_INFO_STREAM("Starting the 2d_camera component again ...");
    cmi_.startComponent("2d_camera");
  }

and register the custom recovery routine in the ``executeTemotoAction()``

.. code-block:: c++

  void executeTemotoAction()
  {
    // Initialize the component manager interface
    cmi_.initialize(this);

    /*...*/

    // Register the custom component recovery routine you have created before
    TEMOTO_INFO("Registering a component status callback ...");
    cmi_.registerComponentStatusCallback(&TaYourActionName::componentStatusCb);

    /*...*/
  }


5) Compile and test the action
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now the whole action should look something like this: 

.. code-block:: c++

  #include <class_loader/class_loader.h>
  #include "ta_your_action_name/temoto_action.h"
  #include "ta_your_action_name/macros.h"
  #include "temoto_component_manager/component_manager_interface.h"

  /* 
   * ACTION IMPLEMENTATION of TaYourActionName 
   */
  class TaYourActionName : public TemotoAction
  {
  public:

  // Constructor. REQUIRED BY TEMOTO
  TaYourActionName()
  {
    // ---> YOUR CONSTRUCTION ROUTINES HERE <--- //
    std::cout << __func__ << " constructed\n";
  }

  // REQUIRED BY TEMOTO
  void executeTemotoAction()
  {
    // Initialize the component manager interface
    cmi_.initialize(this);

    // Register the custom component recovery routine you have created before
    TEMOTO_INFO("Registering a component status callback ...");
    cmi_.registerComponentStatusCallback(&TaYourActionName::componentStatusCb);

    // A structure for containing component topic related information
    ComponentTopicsReq requested_topics;

    // Specify what kind of topics the component should output. These are the same as
    // outlined in the components.yaml file
    requested_topics.addOutputTopicType("camera_data");

    // Debug information
    TEMOTO_INFO("Starting the 2d_camera component ...");

    /*
     * Invoke the "startComponent" method. This method requires the type of the sensor (2d_camera)
     * and will output a structure that maintains the topic names which were received based 
     * on the request
     */
    ComponentTopicsRes responded_topics = cmi_.startComponent("2d_camera", requested_topics);

    // Get the name of the topic where camera feed is published
    std::string camera_data_topic = responded_topics.getOutputTopic("camera_data");

    // Debug information
    TEMOTO_INFO_STREAM("Got camera data on topic '" << camera_topic << "'");
  }

  // Destructor
  ~TaYourActionName()
  {
    // ---> YOUR CONSTRUCTION ROUTINES HERE <--- //
    TEMOTO_PRINT_OF("Destructor", getUmrfPtr()->getName());
  }

  /**
   * @brief A custom component failure recovery routine. You can implement whatever you like
   * in here, such as logging, system rollbacking, starting an alternative component ... 
   */
  void componentStatusCb(const temoto_component_manager::LoadComponent& comp_srv_msg)
  {
    TEMOTO_WARN_STREAM("Received a status message:\n" << comp_srv_msg.request);
    TEMOTO_INFO_STREAM("Starting the 2d_camera component again ...");
    cmi_.startComponent("2d_camera");
  }

  temoto_component_manager::ComponentManagerInterface<TaYourActionName> cmi_;

  }; // TaYourActionName class

  /* REQUIRED BY CLASS LOADER */
  CLASS_LOADER_REGISTER_CLASS(TaYourActionName, ActionBase);

* Compile the Action (``catkin build``).
* Test the action:

  In the first terminal:

  .. code-block:: bash

    roslaunch your_temoto_workspace temoto.launch temoto_namespace:=my_wakeword

  In the second terminal:

  .. code-block:: bash

    roslaunch your_action_name action_test_separate.launch wake_word:=my_wakeword

* Test the recovery behaviour. If you are using a non integrated camera, then you can test the recovery behaviour simply by unplugging 
  and plugging the camera back in again. If you are using an integrated camera then you can test the recovery behaviour by
  killing the usb_cam process by ``killall usb_cam`` or get the PID by ``ps -A | grep usb_cam`` and kill by ``kill PID``. 

Describing and using Pipes
--------------------------

In order to create and start using the pipes, a *pipes.yaml* file has to be created.

1) Create a *pipes.yaml* file 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can create this file where ever in your catkin workspace but for the sake of having a clean workspace, create it in your
TeMoto Workspace root directory.

.. note:: Follow the *"Creating TeMoto workspace*" tutorial if you haven't created a TeMoto workspace yet.

If you aready have the *pipes.yaml* file then great, you can just append it.

**So in your *pipes.yaml* file, outline what's the:**