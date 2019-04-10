---
title: Actions
layout: default
---

<br><br>

# Actions
<hr>
[Semantic Frames](#semantic_frames)<br>
[Action Source](#action_Source)<br>
[Action Packages](#action_packages)

<br>
## Semantic Frames

A Semantic Frame (SF) is a compact semantic representation of an event or situation which in the implementation of TeMoto contains the following information (Fig. 1):
* ***action*** - type of the situation
* ***lexical unit*** - verbs that invoke the SF
* ***object*** - element that describes the situation
* ***object data*** - Data attributed to the object
* ***interface*** - indicates which objects are expected as input/output to the SF

The SF implementation in TeMoto 2.0 describes the context of a single action, that is invoked by a lexical unit. For example a sentence *“start the camera and show it on the screen”* can be represented via 2 SFs shown in Fig. 1. The structure of TeMotos SF is designed to maximize code reuse (via inclusion of interfaces) and make the inter-SF data exchange as simple as possible. Thus the TeMoto SFs might make a linguistically savvy person cringe due to inclusion of non-linguistical terminology. 

<p align="center" >
  <img width="500" src="/temoto-telerobotics.github.io/site/doc/images/agent/semantic_frame_example.png">
  <br>
  <b> Fig. 1: </b> Example of a sentence represented via 2 semantic frames. 
</p>

### Objects
In TeMoto, the term "object" broadly represets parts of a sentence that the subject (mostly TeMoto itself) acts upon. For example sencetce *"Put the box on the table"* contains 2 objects: *"the box"* and *"the table"*. The objects can be differentiated by the questions they answer to, such as: WHAT: *"the box"*; WHERE: *"the table"*. Currently TeMotos SFs only support 3 object types:
* ***what***
* ***where***
* ***numeric***

### Object Data
In addition to the word that the object contains, the object can also contain data that is attributed to the given object. For instance in Fig. 1, the second SF, which expects a noun *"camera"*, is also expecting information (data) about how to receive the camera feed. The data could be just a ROS topic (a string) to the camera feed, or it could be something more specific, such as a pointer to raw image data. In principle the type of the data can be user defined, but TeMoto has few commonly used and hence predefined datatypes:
* ***string*** (std::string)
* ***number*** (double)
* ***topic***  (std::string)
* ***udef***   (user defined)

### Interfaces
Interface is a collection of objects that are expected as an input to the SF, and objects that can be expected as an output. In Fig. 1, the interface of the first SF contains both, input and an output. The second SF in Fig. 1 contains only an input, since that action does not output any information to other potentially proceeding SFs (e.g., the instruction in Fig. 1 could be followed by *"... and dim the screen."*). An interface can be either:
* ***synchronous*** - when the action finishes, it will be automatically stopped (removed from memory)
* ***asynchronous*** - when the action finishes, it will stay in the memory and can serve callback functions (if it has any). An action which was invoked via asynchronous interface has to be stopped manually via *"stop"* instruction.

An SF could contain multiple interfaces where each has its own input/output section (Fig. 2). In this case, the SF can be invoked via a specific interface. So Fig. 2a represents an action that can deal with multiple object types, that is, it can place apples to bowls; boxes to hardcoded destinations; etc. Fig. 2b shows the interfaces of the Fig. 2a segregated into separate SFs.

It is solely up to the user of TeMoto if he/she wants to create multiple actions with the same lexical unit (Fig. 2b) or create multiple interfaces for the same action (Fig. 2a). Both approaches are functionally equivalent.

<p align="center" >
  <img width="500" src="/temoto-telerobotics.github.io/site/doc/images/agent/semantic_frame_example_2.png">
  <br>
  <b> Fig. 2: </b>  a) Example of a SF that contains multiple interfaces; b) Interfaces of the same SF segregated into separate SFs. Both approaches are functionally equivalent.
</p>

<br>
## Action Source

Action Source (AcS) is the implementation of a SF. In broader picture, SFs are used by TeMoto's [Agent](/temoto-telerobotics.github.io/site/managers/agent) for mapping input natural language sentences to executable AcSs. An AcS can contain arbitrary user libraries and code, given that the structure of the AcS follows the [predefined format](#todo). Specifically, AcS is as a c++ Class which:
* implements the SF structure,
* embeds the user written code,
* inherits bunch of methods and parameters from a base AcS Class that are required by Temoto,
* is converted to a dynamically loadable plugin.

Hence the AcS is nothing more than a c++ class which has a structure that corresponds to its SF. A base structure for an AcS can be easily generated with TeMoto's [Semantic Frame Assistant](#todo) but it's up to the user/developer to fill the blank AcS with appropriate code.

The AcSs are not embedded or compiled into TeMoto, but are dynamically loaded upon a request during run-time. This means that AcSs can be added or removed from a running instance of TeMoto.

<br>
## Action Packages

A TeMoto action is structured as a ROS Package ([example](https://github.com/UTNuclearRoboticsPublic/temoto-telerobotics.github.io/tree/master/tasks/ai_show_env)):

* CMakeLists.txt
* package.xml
* semantic_frame.xml
* **src**/action_source.cpp
* **lib**/lib_action_source.so


