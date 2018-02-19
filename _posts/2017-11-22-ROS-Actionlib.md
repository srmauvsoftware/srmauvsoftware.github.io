---
title: Actionlib
date: 2017-11-22 23:29:09
categories:
- Robotics Operating System
- Actionlib
tags:
- Mission Planner
- Actionlib
- SRMAUV
---

Robotics Operating System provides actionlib package which is a robust implementation to perfrom tasks in a robot, I implemented Actionlib in Autonomous Underwater Vehicle's software stack to perfrom goals under water with logging states.  

## Actionlib Usecase
To perform particular tasks in robots we have cases which include sending a request to a node, and also receive a reply to the request.
We can achieve this using ROS Services but in some cases if the service takes a long time to execute, the user might want the
ability to cancel the request during execution or get periodic feedback about how the request is progressing. The actionlib package provides -
* Tools to create servers that execute long-running goals that can be preempted.
* Client interface to send requests to the server.  

Take an example of an underwater vehicle which needs to reach a particular depth. Say from 5m to 10m. So with the DepthServer running in ROS Environment, any client can send
depth goals to the server stating that the vehicle needs to go to a depth of 10m.

![ROS Server](http://wiki.ros.org/actionlib?action=AttachFile&do=get&target=client_server_interaction.png)  

---  

## Actionlib Message:  

The ActionClient and ActionServer communicate via a "ROS Action Protocol". In order for the client and server to communicate, we need to define action specific messages.

Message Format:
```
# Define the goal
float32 depth_setpoint
---
# Define the result
float32 depth_final
---
# Define a feedback message
float32 depth_error
```

## Actionlib CMake Settings
These files are placed in a package's ./action directory, lets call it DepthAction.action and the CMakeLists.txt must be updated as
```
find_package(catkin REQUIRED genmsg actionlib_msgs actionlib)
add_action_files(DIRECTORY action FILES Depth.action)
generate_messages(DEPENDENCIES actionlib_msgs)
```
Further package.xml must be updated as
```
<build_depend>actionlib</build_depend>
<build_depend>actionlib_msgs</build_depend>
<run_depend>actionlib</run_depend>
<run_depend>actionlib_msgs</run_depend>
```

This would generate following messages:
* DepthAction.msg
* DepthActionGoal.msg
* DepthActionResult.msg
* DepthActionFeedback.msg
* DepthGoal.msg
* DepthResult.msg
* DepthFeedback.msg

These messages are then used internally by actionlib to communicate between the ActionClient and ActionServer.  

---
## ActionClient
Further we need to write ActionServer which keeps searching for ActionClients.

* [ActionServer](http://wiki.ros.org/actionlib_tutorials/Tutorials/Writing%20a%20Simple%20Action%20Server%20using%20the%20Execute%20Callback%20%28Python%29) - Receives the goal from client, tracks its status, performs operation,  and return the results. It also checks if user requested preempts.
* [ActionClient](http://wiki.ros.org/actionlib_tutorials/Tutorials/Writing%20a%20Simple%20Action%20Client%20%28Python%29) - Sends a goal to the ActionServer, can be used in GUIs and Mission Planners to send particular goals in manned and autonomous systems.  
Here's my implementation of DepthServer and DepthClient in Autonomous Underwater Vehicle -  

[Alpheus Action Server]()
[Alpheus Action Client]()
