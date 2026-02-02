# Avulal-Origin-One-SLAM-Mapping-and-Autonomous-Navigation

# The following startup sequence should be performed to start the simulation environment and nav2 in SLAM mode. Keep in mind that a new terminal should be used for each topic:

# Launch Gazebo World

ros2 launch origin_one_gazebo sim_greenhouse.launch.py drive_configuration:=mecanum_drive 


# Convert 3D PointCloud to 2D Laserscan

ros2 run pointcloud_to_laserscan pointcloud_to_laserscan_node \
  --ros-args \
    -p use_sim_time:=true \
    -p target_frame:=base_link \
    -p transform_tolerance:=0.2 \
    -p min_height:=0.25 \
    -p max_height:=0.6 \
    -p range_min:=0.3 \
    -p range_max:=12.0 \
    -r cloud_in:=/robot/lidar/points \
    -r scan:=/scan


# SLAM

ros2 launch origin_one_nav2 bringup_slam.launch.py


# RViz

rviz2 -d install/origin_one_nav2/share/origin_one_nav2/rviz/nav2_default_view.rviz


# Teleop for driving the robot manually

ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/robot/cmd_vel


# The following startup sequence should be performed to start the simulation environment and nav2 for autonomous navigation. Keep in mind that a new terminal should be used for each topic:

# Launch Gazebo World

ros2 launch origin_one_gazebo sim_greenhouse.launch.py drive_configuration:=mecanum_drive 


# Convert 3D PointCloud to 2D Laserscan

ros2 run pointcloud_to_laserscan pointcloud_to_laserscan_node \
  --ros-args \
    -p use_sim_time:=true \
    -p target_frame:=base_link \
    -p transform_tolerance:=0.2 \
    -p min_height:=0.25 \
    -p max_height:=0.6 \
    -p range_min:=0.3 \
    -p range_max:=12.0 \
    -r cloud_in:=/robot/lidar/points \
    -r scan:=/scan
    

# Create a Picking Zone Visual in RViz

ros2 run picking_zone_marker picking_zone_marker \
  --ros-args \
  -p frame_id:=base_link \
  -p center_x:=-0.15 \
  -p radius_outer:=0.70 \
  -p radius_inner:=0.30


# Autonomous Navigation (Nav2)

ros2 launch origin_one_nav2 bringup.launch.py


# Convert /cmd_vel/ to /robot/cmd_vel/

ros2 run cmd_vel_relay cmd_vel_relay


# Custom Python Path Planning Script

ros2 run origin_one_custom_nav simple_global_planner \
  --ros-args --params-file src/origin_one_custom_nav/config/custom_nav.yaml


# Custom Python Controller Script

ros2 run origin_one_custom_nav simple_controller \
  --ros-args --params-file src/origin_one_custom_nav/config/custom_nav.yaml


# Send Specified Goal

ros2 topic pub /goal_point geometry_msgs/PoseStamped "
header:
  frame_id: 'map'
  stamp: {sec: 0, nanosec: 0}
pose:
  position: {x: 3.0, y: 0.0, z: 0.0}
  orientation: {x: 0.0, y: 0.0, z: 1.0, w: 0.0} 
" -1


# RViz

rviz2 -d install/origin_one_nav2/share/origin_one_nav2/rviz/nav2_default_view.rviz

