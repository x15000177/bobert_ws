# BOBERT-ARM-CONTROL
Team Mission Control's Repository for arm testing
- Xuanhao Shi

## Design Goals
### Top-Level Goal:
The goal for this specific repository is to develop the hardware interface to connect the physical robot "Bobert"'s hardware to virtual key location points
for *Mission Control*, designed and built by the *UF IEEE Hardware Team*, to compete at *IEEE SoutheastCon*.

### What are the objectives specified by the parent project repository?
The objective of this repository is to create a simulation environment for the control conponents of the robot to be tested,
and then integrate the robotic functions to the actual hardware.

### How does this repository plan to accomplish those goals?
Given the envrionment provided (Ubuntu 18.04 on a Nvidia Jetson with ROS Melodic), this repository handles the following:
- Install and integrate the [MoveIt!](http://docs.ros.org/en/melodic/api/moveit_tutorials/html/doc/getting_started/getting_started.html) package on the OS.
- Set up a [RoboWare](https://github.com/TonyRobotics/RoboWare) envrionment for the workspace.
- Generate a URDF file for our 6 DOF robotic arm from a CAD model.
  - Shown [here](./src/BobertLimits)
- Use the MoveIt! [Setup Assistant](http://docs.ros.org/en/melodic/api/moveit_tutorials/html/doc/setup_assistant/setup_assistant_tutorial.html) to create configuration files to be used by the simulation.
  - Shown [here](./src/bobert_moveit_config)
- Use the [ROS control boilerplate](http://wiki.ros.org/ros_control_boilerplate) as the base for the overall control nodes and topics.
- Write the Bobert control package.
  - Shown [here](./src/bobert_control)
- Write the simulation tests to test the control package.
  - Included in the control package.
- Integrate the whole hardware interface to the Teensy.
- Write the Teensy subscribers to take in the ROS messages.
  - Shown [here](./teensy)
- Write the Jetson ROS launch files to run all the ros packages.'
  - Shown [here](./src/teensy_arm/launch)
- Write the Jetson ROS publishers to transmit the ROS messages.
  - Shown [here](./src/teensy_arm/src)
 

### Software Overview
1. Tools Used & Their Purpose
   - SolidWorks with URDF plug-in: to create the CAD file and convert it to a URDF file for the robotic arm.
   - MoveIt Setup Assistant: to generate the configuration files for the arm.
   - RoboWare: to set up the envrionment and organize all the source code:
      - cfg files, include files, launch files, msg files, src files, CMakeLists, packages, etc.
   - RVIZ: to simulate the control package.
   - Arduino IDE: to program the Teensy. 
2. Relationship Diagram
   - See [here](https://drive.google.com/file/d/1DbsKaMfpr8qT6v7UN-TFkli0K3jzfaP3/view?usp=sharing)
3. Interfaces
   - See [here](https://drive.google.com/file/d/1lojEnnw9Ap1IzZ4uBlDvkBp3bLHRttGS/view?usp=sharing)

### Xuanhao's Responsibilities
  - Using all the tools necessary and writing the hardware interface to simulate and control the arm and wheels from ROS packages.
  - Everything in this repository.


## Progress Log
### Goal:
Writing the Bobert control package.
### Steps Taken [as of Alpha Build 1/21/22]:
- Set up MoveIt! on RoboWare.
- Generated the URDF of the arm from the CAD model.
- Made MoveIt! configuration files.
- Wrote a majority of the src files for the arm model so that it can be loaded into RVIZ.
- See [here](https://drive.google.com/file/d/1r5v7SpiCPZid2jlV856fBVjpEdzBG8-B/view?usp=sharing)

### Issues Present [as of Alpha Build 1/21/22]:
- The Bobert control package currently cannot recognize any of the joint controllers.
- It also cannot load any messages being sent and received through the nodes.


### Goal:
Writing the simulation tests.
### Steps Taken [as of Alpha Build 1/21/22]:
- Finished writing an echo node that will act as the Teensy board to subscribe and publish corresponding messages.

### Issues Present [as of Alpha Build 1/21/22]:
- The exact messages cannot be seen through the terminal due to some of the controllers are unable to be recognized.


### Goal:
Writing the arm movement interface.
### Steps Taken [as of Release Candidate 3/1/22]:
- Finished writing the Teensy node and the Jetson node to publish and subscribe arm servo position messages.

### Issues Present [as of Release Candidate 3/1/22]:
- The arm movement currently is a bit janky and some servo angle limits need to be adjusted to avoid self-collision.

### Current and Future Testing Plan:
- Currently, the test is embedded inside the src files for the bobert_control package.
- To launch the project with tests, do the following:
  - source the workspace with ```source ./devel/setup.bash```
  - launch the main hardware interface ```roslaunch ./src/bobert_control/launch/bobert_HW_main.launch```
- The main hardware interface will load the arm into the RVIZ and then subscribe to the ```/teensy/bobertTelemetry``` topic and publish the  current arm angles to ```/teensy/armCmd```
- This interface will also load the testing node within ```./src/bobert_control/src/bobert_sim_echo.cpp```
- This testing node will simulate a node that works like a teensy controller.
- This testing node will subscribe to the ```/teensy/armCmd``` topic and echo the angle positions to ```/teensy/bobertTelemetry```
- And thus we can use ```rostopic info``` to see the message about the joint controller between the hardware interface node and the teensy node.
- As such, inside RVIZ, we can add whatever planning group we want to modify on the arm, and then plan the path to communicate the information through the terminal in the same format as the ```./src/bobert_control/msg/```
- In the future, we can add more complex msg structures and inputs from the simulated teensy node to be sent to the main hardware interface.
