# Overview

For the develeopment of gopher, it would be advantageous if we were able to simulate the robot in the physics engine gazebo.

This is a package containing a running simulation of the gopher chest_stand component. This code was edited based on the fusion2urdf fusion360 plugin with some minor edits to work for **Noetic**.

## Updates

- Connected the stand to the mobile base platform - fetch freight research platform

# TODO

- Section detailing:
    - required packages for this to run
    - how to install the ros package
    - how to test and debug

# Launch Files

This package contains 3 different launch files of interest:
- display.launch
- gazebo.launch
- controller.launch

**Dependent ROS Packages**
- fetch_gazebo
- fetch_ros

We will go over their purpose right now.

## display.launch

This file loads the robot into rviz. This is used to make sure the robot was converted correctly from the plugin fusion2urdf. If there is no issues with the installation of the package, then there should be no errors.

To launch the file in your terminal run:

```
roslaunch stand_chest_description display.launch
```

Rviz should load with the stand_chest in the resting position (where the chest are at the lowest point 0.0).

## gazebo.launch

This file loads the robot into gazebo. As such we can see how the robot may act in the physics simulator (which slightly translates to the real world).

To launch the file in your terminal run:

```
roslaunch stand_chest_description gazebo.launch
```

As a side note, the first time gazebo runs, it may take 1 min to load. Subseqent launches of this file shoudl load quickly. 

The stand_chest will be spawned into roughtly the center of a blank world.

## controller.launch

This file loads both the robot_state_publisher and the joint_effort_controller for the robot. This controller is configured to work ONLY in sim. 

To launch the file in your terminal launch gazebo and then the controllers:

```
roslaunch stand_chest_description gazebo.launch
roslaunch stand_chest_description controller.launch
```

To check that the controller was launched successfully run:

```
rostopic list | grep "stand_chest"
```

The following should be seen:

```
/stand_chest/Zaxis_position_controller/command
/stand_chest/Zaxis_position_controller/pid/parameter_descriptions
/stand_chest/Zaxis_position_controller/pid/parameter_updates
/stand_chest/Zaxis_position_controller/state
/stand_chest/joint_states
```

As previously mentioned, this file runs:
- robot_state_publisher
- effort_joint_controller

The robot_state_publisher is renamed as /stand_chest/joint_states (check remap for more information). It reports the joint(s), effort(s), position(s), and velocity(s).

The effort_joint_controller handels the control of the joint, using the controller_manager ros package. We will be using this controller to move the chest up and down.

It is important to note that this joint is a position controller, where the controller will move the chest to target positions. 

## Controlling the stand_chest

To move the chest, in seperate terminals launch both the gazebo simulation and the controllers

```
roslaunch stand_chest_description gazebo.launch
roslaunch stand_chest_description controller.launch
```

Now, to send the chest to a target (desired) position, run:

```
rostopic pub /stand_chest/Zaxis_position_controller/command std_msgs/Float64 "data: 0.0" 
```

where data: 0.0 sends the robot to zero. 

Valid inputs for this joint are between 0.0 and 0.47.  
