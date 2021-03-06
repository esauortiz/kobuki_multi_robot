# kobuki_multi_robot ROS package

This package includes ROS launch files for starting up kobukis with their respective sensors (RealSense, Hokuyo or RPLIDAR). The required packages for each kobuki's NUC are assumed to be installed.

## How to build kobuki_multi_robot package
Copy this repository to your catkin workspace [src](https://github.com/esauortiz/kobuki_multi_robot/tree/master/src) folder:
 
```bash
git clone https://github.com/esauortiz/kobuki_multi_robot.git
```

Build catkin_ws at ~/catkin_ws directory:
 
```bash
catkin_make
```

------------------------------------------------------------
## Usage in real environment

### Launch base station (on base station)
Launch kobuki description, rviz and goal master publisher. Goal master publications are triggered by ```rostopic pub /update_global_goals std_msgs/Int8 "data: 1"``` (see README file in [src](https://github.com/esauortiz/kobuki_multi_robot/tree/master/src) folder).

```bash
roslaunch kobuki_multi_robot base_station.launch map_name global_goals_file_name
```
See available maps in [maps](https://github.com/esauortiz/kobuki_multi_robot/tree/master/maps) folder and see available global goals files in [global goals](https://github.com/esauortiz/kobuki_multi_robot/tree/master/param/global_goals) folder.

### Launch kobuki and its sensor (on kobuki)
Launch kobuki and its sensor.

```bash
roslaunch kobuki_multi_robot kobuki.launch robot_name sensor
```
* Kobuki name is specified with ```robot_name``` parameter and can take the following values: ```kobuki_a```, ```kobuki_b```, ```kobuki_c```, ```kobuki_d``` and ```kobuki_e```.

* Sensor parameter specifies the ```sensor``` placed on the kobuki and can take the following values: ```realsense```, ```hokuyo04LX```, ```hokuyo20LX```, ```hokuyo30LX``` and ```rplidar```.

This roslaunch file could be also run with the next .sh file placed in [start_and_nav](https://github.com/esauortiz/kobuki_multi_robot/tree/master/start_and_nav) folder.

```bash
./kobuki_start.sh
```

Each kobuki has environmental variables defined in the .bashrc file in order to complete ```robot_name``` and ```sensor``` arguments.

### Launch kobuki navigation (on kobuki)

Launch [navigation stack](http://wiki.ros.org/navigation) and goal listener node (subscribed to goal master publisher, publishes goals for kobuki if there is some goals with ```kobuki_id```).

	roslaunch kobuki_multi_robot kobuki_navigation.launch robot_name kobuki_id mission_label
	
* Kobuki name is specified with ```robot_name``` parameter and can take the following values: ```kobuki_a```, ```kobuki_b```, ```kobuki_c```, ```kobuki_d``` and ```kobuki_e```.

* Kobuki id is specified with ```kobuki_id``` parameter as an integer value.

* [AMCL](http://wiki.ros.org/amcl) node requires an initial kobuki pose specified with ```init_pose_x```, ```init_pose_y``` and ```init_pose_a``` (x, y and yaw). This arguments should be defined in [param/missions](https://github.com/esauortiz/kobuki_multi_robot/tree/master/param/missions) folder and a ```mission_label``` should be provided.

This roslaunch file could be also run with the next .sh file placed in [start_and_nav](https://github.com/esauortiz/kobuki_multi_robot/tree/master/start_and_nav) folder.

```bash
./kobuki_navigation.sh --mission mission_label
```

Each kobuki has environmental variables defined in the .bashrc file in order to complete ```robot_name``` and ```sensor``` arguments. The mission (option -m|--mission) has to be specified in order to provide an initial pose for each kobuki, see available missions in [missions](https://github.com/esauortiz/kobuki_multi_robot/tree/master/param/missions) folder.

------------------------------------------------------------
## Usage in simulation

### Launch base station (on base station)
Launch kobuki description, rviz and goal master publisher. Goal master publications are triggered by ```rostopic pub /update_global_goals std_msgs/Int8 "data: 1"``` (see README file in [src](https://github.com/esauortiz/kobuki_multi_robot/tree/master/src) folder).

```bash
roslaunch kobuki_multi_robot base_station.launch map_name global_goals_file_name
```
See available maps in [maps](https://github.com/esauortiz/kobuki_multi_robot/tree/master/maps) folder and see available global goals files in [global goals](https://github.com/esauortiz/kobuki_multi_robot/tree/master/param/global_goals) folder.

### Launch gazebo with environment, kobuki and its sensor (on base station)
Launch similation environment. The number of kobukis and their name have to be specified in ```simulated_playground.launch``` file.

```bash
roslaunch kobuki_multi_robot simulated_playground.launch world_label
```

See available worlds in [worlds](https://github.com/esauortiz/kobuki_multi_robot/tree/master/simulation/environment/worlds) folder.

### Launch navigation stack for kobukis in gazebo (on base station)
Launch [navigation stack](http://wiki.ros.org/navigation) for each kobuki in similation. Additionally, a mission has to be specified in order to place kobukis in simulation. The number of kobukis, their name and their ID have to be specified in ```simulated_mission.launch``` file.

```bash
roslaunch kobuki_multi_robot simulated_mission.launch mission_label
```

See available missions in [missions](https://github.com/esauortiz/kobuki_multi_robot/tree/master/param/missions) folder.

### Mission example
This package provides a default mission and a file with global goals. Running simulation launch files with ```mission_label:=default``` and ```map_name:=srvlab``` will set up default mission. In order to trigger global goals publications run: 

```bash 
rostopic pub /update_global_goals std_msgs/Int8 "data: 1"
```

The execution of the mission should look like the following representation, although the trajectories may vary.

![](https://github.com/esauortiz/kobuki_multi_robot/blob/master/documentation/pictures/mission_default.gif)

Changing arguments to ```map_name:=willowgarage```, ```global_goals_file_name:=willowgarage.txt```, ```world_label:=willowgarage``` and ```mission_label:=willowgarage``` will set up willowgarage mission. In order to trigger global goals publications run:

```bash 
rostopic pub /update_global_goals std_msgs/Int8 "data: 1"
```

The execution of the mission should look like the following representation, although the trajectories may vary. Notice that hokuyo range has been increased to improve kobuki's pose estimation.

![](https://github.com/esauortiz/kobuki_multi_robot/blob/master/documentation/pictures/mission_willowgarage.gif)

------------------------------------------------------------
## Some usage considerations
* This package provides two ```bash``` files called ```kobuki_start.sh``` and ```kobuki_navigation.sh``` placed in [start_and_nav](https://github.com/esauortiz/kobuki_multi_robot/tree/master/start_and_nav) folder to make deployment of the kobukis easier. Each kobuki has environmental variables defined which are used in these files. Please, execute ```kobuki_start.sh``` before ```kobuki_navigation.sh --mission mission_label``` with a given mission label (see [missions](https://github.com/esauortiz/kobuki_multi_robot/tree/master/param/missions/) folder).
* ```kobuki_d```has a RPLIDAR mounted. This sensor provides a range of 360 degrees but the scans placed in the 'back' of the kobuki have been 'removed' by means of [laser filter](http://wiki.ros.org/laser_filters) node. However, the messages published in the topic ```/kobuki_d/scan``` contain 360 degrees and the 'removed' scans have values of ```range_max + 1```, which is assumed to be an error case.

## Troubleshooting

- The first time the navigation stack nodes are launched the cost map does not update properly and will always be 'empty'
	- Solution: Run ```kobuki_navigation.sh --mission <mission_label>```, let everything be initialized until you get to the ```odom received!``` message in your terminal, shutdown the execution and run the same file again.  In this way the cost map is updated correctly and the navigation stack runs properly.