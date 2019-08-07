---
title: Actions
layout: default
---

<br><br>

# Actions
<hr>
[**UMRF**](#universal-meaning-representation-fromat)<br>
[ • UMRF JSON Format](#umrf-json-format)<br>
[ • Parameter Flattening](#parameter-flattening)<br>
[ • Parameter Value Format](#parameter-value-format)<br>
[ • Parameter Source](#parameter-source)<br>

[**TeMoto Actions**](#temoto-actions)<br>
[ • Design Principles](#design-principles)<br>

<br>
## Universal Meaning Representation Fromat

Universal Meaning Representation Format (UMRF) outlines natural language structures through input-output parameters and by specifying relations between other UMRF structures. In short, UMRFs comprise graphs that can describe complex concepts and interactions.

The motivation behind UMRF is to define a generic, low-overhead and flexible format for embedding different types of Meaning Representation (MR) structures (such as Alexa Meaning Representation Language or Google's Dialogflow webhook format). Unified format allows to mix and match different cutting edge natural language parsing and action execution tools.

### UMRF JSON Format

UMRF is specified in JavaScript Object Notation (JSON), a language-independent data format which facilitates integration with a variety of software platforms. The general outline of the UMRF format is the following:

```json
{
  "name": {},
  "input_paramteters": {},
  "output_parameters": {},
  "suffix": {},
  "effect": {},
  "parents":[
    {"name": {}, "suffix": {}}
  ],
  "children":[
    {"name": {}, "suffix": {}}
  ],
}
```

where common attributes are
* ***name*** - name of the action
* ***input_parameters*** - necessary input information for the action
* ***output_parameters*** - output parameters that the action provides 

and application/context specific parameters are
* ***suffix*** - a number which differentiates actions, when multiple instances of the same action are present in the same action graph
* ***effect*** - indicates whether or not the action is supposed to function even after its main execution routine is finished
* ***parents*** - when the action is part of a UMRF graph, then this field indicates the preceding actions
* ***children*** - when the action is part of a UMRF graph, then this field indicates the proceeding actions


### Parameter Flattening

Parameter flattening is conversion of nested structures to a key-value list where the key embodies the nested structure of a specific parameter. Each nest level is denoted by nesting namespace "::". For example: 

Unflattened
```json
"input_paramteters": {
  "params_0":{
    "param_0": "value",
    "param_1": "value",
  },
  "params_1":{
    "sub_params_0":{
      "param_0": "value",
      "param_1": "value",
    },
    "param_1": "value",
  },
  "param_0": "value"
}
```

Flattened
```json
"input_paramteters": {
  "params_0::param_0": "value",
  "params_0::param_1": "value",
  "params_1::sub_params_0::param_0": "value",
  "params_1::sub_params_0::param_1": "value",
  "params_1::param_1": "value",
  "param_0": "value"
}
```

### Parameter Value Format

JSON format defines [3 main data types](https://www.w3schools.com/js/js_json_datatypes.asp): a string, a number and a boolean. While it's good enough for most of applications that use JSON, Actions can do a lot more if the parameters can represent any format of data. It might be over-engineering but languages like C++ are not superficially ambiguous with data like python, and need more info for reliable runtime data conversion and parsing. Hence the Parameter Value Format:

```json
{
  "type":"", 
  "specification":"",
  "value":"",
  "source_id":"",
  "timestamp":"",
  "required":"true/false",
  "updatable":"true/false",
  "guaranteed":"true/false"
}
```

where

* ***type*** (required) is a string that indicates the data type.
* ***value*** (optional) is the value of the data. Value field can be set as a requirement, e.g., action expects a numerical value of "5".
* ***source_id*** (optional) identifies the origin of the parameter.
* ***required*** (optional, default=true) describes if this parameter is required for the action to execute
* ***updatable*** (optional, default=false)  describes if this parameter may be updated externally during the execution of the action
* ***guaranteed*** (optional, default=false) if this parameter is used as an output value, then "guaranteed" indicates whether the parameter is set when the action finishes execution

### Parameter Source

Parameter source is an additional identifier in the input parameter section, which indicates what kind of parameters must be retrieved from a same parameter source.

**Context**: Parameters can be clustered together e.g. a coordinate which is denoted by x, y, z, or parameters can be independent e.g. 2 unrelated parameters such as length of the room and number of people in it. In principle the nuber of parents of an action is not limited, all actions care about is have all required input parameters been retrieved or not. But not caring about where the parameters come from creates a problem: how to make sure that a dependent parameter cluster (such as a coordinate) comes from a single source? It would not make sense if coordinate::x is retreived from one source and coodinate::y from completely different and unrelated source. For that Parameter Sources could come handy

For example:

```json
"input_paramteters": {
  "params_0":{
    "single_source": "true", // All following params must have same source_id
    "param_0": "value",
    "param_1": "value",
  },
  "params_1":{
    "sub_params_0":{
      "param_0": "value",
      "param_1": "value",
    },
    "param_1": "value",
  },
  "param_0": "value"
}
```

### Example

[Amazon AMRL embedded in UMRF](https://share.nuclino.com/p/Example-AMRL-in-UMRF-HT2uUKM2LnAjMAKqbY3rH1)


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

## TeMoto Actions

In a nutshell TeMoto Action is an executable which accepts and outputs parameters as defined by its UMRF. In technical terms, action is a plugin that is described by UMRF. The UMRF file helps the Action Engine to choose the appropriate action. 

An action can contain arbitrary user libraries and code. Specifically, action is as a c++ Class which:
* implements the UMRF structure,
* embeds the user written code,
* inherits bunch of methods and parameters from a base action Class that are required by TeMoto,
* is converted to a dynamically loadable plugin.

Hence an action is nothing more than a c++ class which has a structure that corresponds to its UMRF. A base structure for an action can be easily generated with TeMoto's [Action Assistant](https://temoto-telerobotics.github.io/tutorials/writing_an_action.html) but it's up to the user/developer to fill the blank AcS with appropriate code.

Actions are not embedded or compiled into TeMoto, but are dynamically loaded upon a request during run-time. This means that actions can be added or removed from a running instance of TeMoto.

### Design Principles

TeMoto actions follow the according design principles (Fig. 1):
* The number of parents (indexed by k) and children (indexed by l) of an action "A" is not limited. 
* The number of input parameters (indexed by n) and output parameters (indexed by m) of an action "A" is not limited.
* Action "A" is executable only when all required input parameters are received (see [Parameter Value Format](#parameter-value-format)).
* All actions have an unique name (identifier).

<p align="center" >
  <img width="500" src="/temoto-telerobotics.github.io/site/doc/images/multiple_parent_umrf_action.png">
  <br>
  <b> Fig. 1: </b>  TeMoto Action design principles
</p>

