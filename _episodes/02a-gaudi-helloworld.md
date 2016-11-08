---
title: "Hello world the Gaudi framework"
teaching: 20
exercises: 30
questions:
- "How do I make a simple Gaudi program?"
objectives:
- "Learn how to write a simple test Gaudi program."
keypoints:
- "Hello world in Gaudi."
---

## Hello World in Gaudi

The following is a minimum example to build an algorithm in Gaudi as a new package.

First, you will need to set up a new package. Here is an example `CMakeLists.txt` for a new LHCb package:

```cmake
CMAKE_MINIMUM_REQUIRED(VERSION 2.8.5)

find_package(GaudiProject)

gaudi_project(HelloWorld v1r0
    USE Gaudi v27r1)

gaudi_add_library(GaudiHelloWorld *.cpp
    LINK_LIBRARIES GaudiKernel
    NO_PUBLIC_HEADERS)
    
include_directories(.)
```

This first sets the CMake version and loads the GaudiProject CMake module. Then a new project (HelloWorld) is declared, and Gaudi is set as a dependency. 
The library we are making is added as GaudiHelloWorld, linked to the Gaudi Kernel, and no headers are exported. Finally, the current directory is added to the CMake includes.

To create an algorithm, the following header file is used:

```cpp
#pragma once

#include "GaudiKernel/Algorithm.h"
#include "GaudiKernel/Property.h"
#include "GaudiKernel/MsgStream.h"

class HelloWorld : public Algorithm {
public:
    HelloWorld(const std::string& name, ISvcLocator* pSvcLocator); 
    StatusCode initialize() override;
    StatusCode execute() override;
    StatusCode finalize() override;
    StatusCode beginRun() override;
    StatusCode endRun() override;

private:
    bool m_initialized;
};
```

The implementation is simple:

```cpp
#include "HelloWorld.h"

DECLARE_COMPONENT(HelloWorld)

HelloWorld::HelloWorld(const std::string& name, ISvcLocator* ploc) :
    Algorithm(name, ploc) {
        m_initialized = false;
    }

StatusCode HelloWorld::initialize() {
  if( m_initialized ) return StatusCode::SUCCESS;

  info() << "initializing...." << endmsg;
  m_initialized = true;
  return StatusCode::SUCCESS;
}

StatusCode HelloWorld::execute() {
  info() << "executing...." << endmsg;
  return StatusCode::SUCCESS;
}

StatusCode HelloWorld::finalize() {
  info() << "finalizing...." << endmsg;

  m_initialized = false;
  return StatusCode::SUCCESS;
}

StatusCode HelloWorld::beginRun() {
  info() << "beginning new run...." << endmsg;

  m_initialized = true;
  return StatusCode::SUCCESS;
}

StatusCode HelloWorld::endRun() {
  info() << "ending new run...." << endmsg;

  m_initialized = true;
  return StatusCode::SUCCESS;
}
```

An example `gaudi_opts.py` options file that uses our algorithm:

```python
from Gaudi.Configuration import *
from Configurables import HelloWorld

ApplicationMgr().EvtMax = 10
ApplicationMgr().EvtSel = "NONE"

alg = HelloWorld()
ApplicationMgr().TopAlg.append(alg)
```

Note the `EvtSel = "NONE"` statement; that sets the input events to none, since we are not running over a real data file or detector.

To run, the following commands can be used:

```bash
$ lb-project-init
$ make
$ ./build.x86_64-slc6-gcc49-opt/run gaudirun.py gaudi_opts.py
```

> ## Code
>
> The code for these files is here:
> 
> * [README.txt](/DevelopKit/code/gaudi/hello_world/README.txt)
> * [CMakeLists.txt](/DevelopKit/code/gaudi/hello_world/CMakeLists.txt)
> * [HelloWorld.cpp](/DevelopKit/code/gaudi/hello_world/HelloWorld.cpp)
> * [HelloWorld.h](/DevelopKit/code/gaudi/hello_world/HelloWorld.h)
> * [gaudi_opts.py](/DevelopKit/code/gaudi/hello_world/gaudi_opts.py)
> 
{: .callout}

> ## Futher reading
> 
> There are further examples in the repoository for [Gaudi/GaudiPython/src/Test](https://gitlab.cern.ch/lhcb/Gaudi/tree/future/GaudiPython/src/Test).
> 
{: .callout}