# WARN
now kino planner is working but topo planner is not perfect

# Fast-Planner for Real Quadrotor with px4

The orignal fast planner is https://github.com/HKUST-Aerial-Robotics/Fast-Planner   
in may case the main algorithm and concept is same but edited for px4 sitl and real flight

**px4**   
My drone use px4 with D435i, T265   
I added and edited some parts to run in px4 and check it in px4_sitl gazebo

# CONCEPT
This is simple   
(1) take off
(2) user - Set the destination   
(3) kino or topo planner gonna make the trajectory   
(4) pub the trajectory to mavros as /mavros/setpoint_raw/local   
(5) then px4 gonna fly   

 

In my case, I used https://github.com/mzahana/px4_fast_planner
and this one use 'movros_controllers', which was unstable in my drone



# INSTALL & SETTINGS
- cd ~/catkin_ws/src # in my case 'catkin_ws' and 'PX4-Autopilot' is located in HOME
- git clone https://github.com/beomsu7/Fast-Planner 
- cd .. && catkin build && source devel/setup.bash
- cd Fast-Planner
- cp storage/1024_iris_depth_camera ~/PX4-Autopilot/ROMFS/px4fmu_common/init.d-posix/airframes/
- cp storage/depth_camera.sdf ~/PX4-Autopilot/Tools/sitl_gazebo/models/depth_camera/
- sudo vim ~/PX4-Autopilot/platforms/posix/cmake/sitl_target.cmake  
>- **# about in line 103~130, add 'iris_depth_camera'**
- cd ~/PX4-Autopilot && DONT_RUN=1 make px4_sitl_default gazebo_iris_depth_camera


# RUN in gazebo
- cd ~/PX4-Autopilot
- source Tools/setup_gazebo.bash $(pwd) $(pwd)/build/px4_sitl_default
- export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)
- export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)/Tools/sitl_gazebo
- roslaunch plan_manage px4_sitl_kino_replan.launch

- rosrun mavros mavsafety arm 
- rosrun mavros mavsys mode -c OFFBOARD   

# RUN in real drone


# ERRORS

There were two main error when I tested and runned these

(1) Fast-planner is not working error
> when clicking and dragging for 2d nav goal then the error go up
> In my desktop the error go up and laptop doesn't
![screensh](./image/fast_planner_error.png)

(2) mavros setpoint not working error in sitl
> if drone arms but not takes off then, I'm not sure but my solution was change the topic from /mavros/setpoint_raw/local to /mavros/setpoint_position/local   
> if you changes it, then the topic doesn't care about acc and vel, only position
> 
> the way to change the setpoint topic is edit the /Fast-Planner/fast_planner/plan_manage/launch/px4_sitl_kino_replan.launch line 78~81
> delete line 78~81 and delete the annotation of line 82~85
