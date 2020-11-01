# ROS to ATAK bridge
## History
- The original implementation used the AI-TF  ros-cot bridge. While the code is still present and works in one direction it is advised to not use it. This is the CPP code in this project.
- The best working implementation is based on work found at: https://github.com/pinztrek/takpak.git.
    - The python code found in the takpak directory is slightly modified versions of this code.
    - The python code found in dcist_cots.py is based on example code found in this repo.
- FX currently the Firewall on the server is turned off. It is blocking ICMP traffic that is required for atak to work.    

# ROS Nodes
## dcist_cots node     
This node is the recommend node to use for DCIST. This node listens to an ATAK server for a move-to message. If one is received the node publishes the latitude, longitude, altitude, and a time stamp.  This node also sends the current position robot position to the ATAK Server.  

#### Example usage
An example of for running this node can be found in the launch directory. To run this use the below command:  
`roslaunch atak_bridge dcist_tak.launch`  

#### Published Topics
- `/atak/atak_tgt` (geometry_msgs/Vector3Stamped): Current location in latitude, longitude, and altitude that a ATAK system is requesting the robot to move to.   

#### Subscribed Topics
- `/atak/atak_fix` (sensor_msgs/NavSatFix): Current location of the robot in the world coordinate frame. This used to update the TAK Server on the current robot position.

#### Parameters
- `callsign` (string, default: 'default_callsign')  
    The call sign used by this system to identify itself on TAK  
- `uid` (string, default: 'fqdn + uuid')  
    A unique identifier used by TAK to identify this system  
- `team_name` (string, default: 'Default Team')  
    This system's team affilation
- `team_role` (string, default: 'Default Team Role')  
    This system's role in the team
- `tak_ip` (string, default: '127.0.0.1')  
    The IP address of the server
- `tak_port` (string, default: '8088')  
    The port for an unsecure connection to the server.

## global_cords_node 
This node exists as a work around for the current state of the Unity simulator. At this time the simulator does not produce world centric coordinates. This node subscribes to the global coordinate system as seen by the robots odometry. It converts this to world centric coordinate system and in effect simulates a GPS. As the robot moves the latitude and longitude published is updated to simulate updating the robots location in a world centric coordinate frame.

#### Example usage
- This node can be ran on the cli with this command:  
`rosrun atak_bridge global_cords_node.py`   
- This node can be included in a launch file with this line:  
`<node name="global_cords" pkg="atak_bridge" type="global_cords_node.py" />`  


#### Published Topics
- `atak_fix` (sensor_msgs/NavSatFix): A simulated location in latitude, longitude, and altitude generated by combining the robots odometry with an initial offset.   

#### Subscribed Topics
- `odom` (nav_msgs/Odometry): The location that the robot currently beleives it is at, in the Unity simulation global coordinate system from the perspective of the robot. This is used to create a simulated gps latitude and longitude.  

## fix_pub node
This node exists to simulate a gps on a moving robot. This node is used for testing without robots or simulation the TAK interaction. It publishes a latitude and longitude at 1 HZ. Each time it publishes it adds a small offset to the current position to simulate movement.

#### Published Topics
- `atak_fix` (sensor_msgs/NavSatFix): A simulated location in latitude, longitude, and altitude generated by adding an offset to the last published coordinate.   




