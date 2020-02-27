Tracing
=======

This tutorial shows how to enable and use a tracer which allows you to see how commands and requests propagate
through different subsystems of TeMoto. Tracer in combination with a trace collector (such as Jaeger) is a great
debugging tool that indicates the exact time and subsystem where a specific event took place. 

About Tracing
-------------
Figure 1 shows an example trace where an action `TaTutStartPipe` invokes the `load_pipe` service in the `component_manager`.
The `load_pipe` service then invokes `load_component` service 2 times (on 8th and 17th millisecond) where both 
send a request to `load_process` service in the `temoto_er_manager`. It can also be seen on the trace that the 
`load_pipe` request took 33.07ms in total and also durations of each span is shown.

.. figure:: /images/tracing/jaeger_trace_example.png
    :width: 650px
    :align: center

    **Figure. 1:** Example of a trace, visualized in Jaeger trace collector GUI. 

So it's quite evident that tracing provides good insight to the behaviour of a complex system, indicating bottlenecks
and request hirearchies.

Enabling The Tracer
-------------------

1) Re-Build TeMoto
~~~~~~~~~~~~~~~~~~
By default TeMoto is compiled without tracing functionalities, so first thing you need to do in order to enable
them is to compile TeMoto with an additional compiler flag:

.. code-block:: bash

  cd your/catkin_workspace/directory
  catkin build --cmake-args -DTEMOTO_ENABLE_TRACING=ON

2) Set-Up the Tracer Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
TeMoto integrates a tracing library called `OpenTracing <https://github.com/opentracing/opentracing-cpp>`_ which
provides the basic tracing functions and the API for a tracer. `OpenTracing <https://github.com/opentracing/opentracing-cpp>`_ is 
an open-source project that has been utilized as a base library by a number of tracing/trace-collector vendors. In this tutorial
we are going to set up `Jaeger <https://www.jaegertracing.io/>`_, an open source tracer that builds upon `OpenTracing <https://github.com/opentracing/opentracing-cpp>`_.

For that we need to build Jaeger manually, since it's technically part of TeMoto and hence is not automatically built. For that do:

.. code-block:: bash

  cd <YOUR/PREFERRED/PATH/FOR/RANDOM/REPOS>
  git clone --branch v0.5.0 https://github.com/jaegertracing/jaeger-client-cpp
  cd jaeger-client-cpp
  mkdir build && cd build
  cmake ..
  make -j

And now you need to tell TeMoto where the it can find the tracer library and how it should be configured. This 
is done via a tracer configuration file which you need to create:

.. code-block:: bash

  roscd temoto_core/config/
  nano tracer_config.yaml

and paste the following content to your `tracer_config.yaml` file:

.. code-block:: yaml

  library_path: <YOUR/PREFERRED/PATH/FOR/RANDOM/REPOS>/jaeger-client-cpp/build/libjaegertracing.so
  disabled: false
  reporter:
    logSpans: true
  sampler:
    type: const
    param: 1

.. note:: You have to point the `library_path` field to the directory where you built the tracing library

3) Set-Up the Trace Collector
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now that the library has been configured, lets set-up the Jaeger client. The simplest way is to use
a Docker container that has Jaeger trace collector server already installed and configured. If you just
heard about Docker, then Google is your friend and ``$ sudo apt install docker``. If Docker has been 
set up, then get the Jaeger Docker container ``docker pull jaegertracing/all-in-one:1.17`` (or get the 
latest version from `here <https://www.jaegertracing.io/download/>`_).

Run the tracer by: 

.. code-block:: bash

  docker run \
    --rm \
    -p 6831:6831/udp \
    -p 6832:6832/udp \
    -p 16686:16686 \
    jaegertracing/all-in-one:1.17 \
    --log-level=debug

.. note:: Use the version of the Docker container you actually installed

and now open your browser and go to ``http://localhost:16686``. Now you should see the Jaeger client interface.

Using the Tracer
----------------

All you need to do now is launch TeMoto and play with the Jaeger GUI.