## **A basic ROS command line controller for the Interbotix PX100 Arm.**

This small program allows for you to get a feel for what the python API for the Interbotix PX100 arm is like and how it works. 

To run this, you will need to have the Interbotix workspace installed somewhere on your computer. 

*In control_arm.py, please change the path on line 12 to the path to the interbotix directory on your computer.*

I recommend running the send_command.py file separately since it will keep things neater in the terminal, however, feel free to add it in if you don't want to launch in two different terminals. It might be a little confusing with all the different text though. 

Launch the launch file and the send_command.py file: 

```
roslaunch arm_control command_center.launch
rosrun arm_control send_command.py
```

Launch the following files if you would like to run everything separately: 

```

roslaunch interbotix_xsarm_control xsarm_control.launch robot_model:=px100 use_sim:=true

rosrun arm_control send_command.py

rosrun arm_control control_arm.py

```

*change the use_sim command to use_actual if you are using the actual arm!*

There are a few commands that are available at the moment: 

    - Enter a point (x,y,z) : p 0.1 0 0 

    - Enter a pose (x,y,z) : m 0.2 0 0.1  ----- this sets the ee_pose_components of the arm

    - Set trajectory time: t 1.0 -----  sets the amount of time it takes for the arm to move to its destination

    - Go to the home position : home 

    - Go to the sleep position : sleep

    - Open the gripper : og

    - Close the gripper : cg

    - Exit the program : exit

### **send_command.py**

This node will run in the command line until the user inputs exit. 

The user can input any of the allowed commands to command the arm to go to the position. 

Depending on the command, the node will publish the appropriate message after checking for proper inputs. 
These messages will be read in the control_arm.py node. 


NOTE:   The y value is actually moving the waist of the arm which uses a different command than the x and z values 
        which sets the joint position. The range is (-3.1, 3.1). If you enter the same value twice, 
        the waist will not move because it is already at the set coordinate.

NOTE:   The x and z coordinates are relative to the current position. So if the current x position is 0.2 and you input 
        0.1 for x, then the new position will be 0.3 not 0.1. The same is true for z. This is unlike the y value which
        is a joint position. 
  

### **control_arm.py**

This node controls the arm based on the messages sent from send_command.py.

You can see whether the command succeeded or not in the terminal. Depending on the values given, a valid pose might not be found. This is a good way to see what are the limits of the robot and get a feel for what works and what doesn't. 

Another thing to consider when you are writing a program that sends messages to this node to control the arm is that each command takes a few seconds to execute so if you send commands without any pauses, they may not be executed because it looks like the messages may be missed. What I found worked was using rospy.sleep(2-3) to make the program sleep for a few seconds between commands. There may be other ways to get around this, but I haven't figured it out as of yet. I think if you are working with the control methods directly rather than through messages, this isn't a problem. 

