---
title: Agent
layout: default
---

<br><br>

# Agent
<hr>
[Back to Supervisory Layer](/temoto-telerobotics.githubub.io/site/layers/supervisory_layer) <br>
[Back to Architecture](/temoto-telerobotics.githubub.io/site/p_conceptual_overview.html#architecture) <br>
<br>

**The Agent** is the core subsystem of TeMoto 2.0, acting as an interface for the operator. It grounds the instructions in Natural Language (NL) into machine executable format which is subsequently executed. The NL grounding framework utilizes and builds upon the concept of [Semantic Frames (SF)](/temoto-telerobotics.githubub.io/site/concepts/actions.html#semantic_frames).

<p align="center" >
  <img width="500" src="/temoto-telerobotics.githubub.io/site/doc/images/nlp_pipeline.png">
  <br>
  <b> Fig. 1: </b> Natural language processing pipeline. 
</p>

The NL grounding flow in TeMoto 2.0 is shown in Fig 1. In idle state, the Agent continuously waits for instructions in a text format. Hence the source can range from an Automatic Speech Recognition (ASR) system to a remote terminal, given that all of the sources output NL as a string of text. The received text is first forwarded to Semantic Parser which extracts potential SFs from the text and arranges them into a SF tree. SF tree represents the hierarchical structure of the given instruction, indicating the chronological order and dependencies of SFs. Next, the SF tree is forwarded to Semantic Frame Finder, which attempts to find a match for the tree of potential SFs via a set of known (developer defined and built-in) SFs. The resolved SF tree is then converted to a executable format meaning that all the SF related shared libraries are loaded and arranged to be sequentially executed based on the structure of the SF tree.

