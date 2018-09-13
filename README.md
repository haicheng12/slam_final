# slam_final
virtual_sensor,ekf and so on.

Required dependent environment:
------------
```
git clone https://github.com/yujinrobot/yujin_ocs.git
sudo apt-get install ros-kinetic-ecl-*
git clone https://github.com/yujinrobot/yocs_msgs.git
sudo apt-get install ros-kinetic-ar-track-alvar
```

Virtual_sensor environment:
-------------

1.Add something useful to the standalone.launch file,and we only use yocs_vitrual_sensor:

```
<launch>
  <arg name="virtual_wall" default="$(find yocs_virtual_sensor)/data/wall_list.yaml"/>
  <node name="virtual_sensor" pkg="yocs_virtual_sensor" type="virtual_sensor_node" >
    <param name="range_min"    value="0.0"/>
    <param name="range_max"    value="6.0"/>
    <param name="frequency"    value="10.0"/>
    <param name="hits_count"   value="3"/>
    <param name="global_frame" value="/map"/>
    <param name="sensor_frame" value="/base_link"/>
  </node>
　　<node pkg="tf" type="static_transform_publisher" name="base_to_virtual" args="0 0 0 0 0 0 /base_footprint /virtual_laser 50"/>
  <node name="wall_publisher" pkg="yocs_virtual_sensor" type="wall_publisher.py" required="true" >
    <param name="virtual_wall" value="$(arg virtual_wall)"/>
  </node>

</launch>
```

2.Next and virtual_sensor to costmap.yaml file,we add another scan in it:

```
obstacle_range: 2.5
raytrace_range: 3.0
footprint: [[-0.3, -0.3], [-0.3, 0.3], [0.3, 0.3], [0.3, -0.3]]
#robot_radius: ir_of_robot
inflation_radius: 0.5
cost_scaling_factor: 10.0
observation_sources: scan1 scan2

scan1: {sensor_frame: base_link,  observation_persistence: 0.0, max_obstacle_height: 0.4, min_obstacle_height: 0.0, data_type: LaserScan, topic: /scan, marking: true, clearing: true}
scan2: {sensor_frame: base_link,  observation_persistence: 0.0, max_obstacle_height: 0.4, min_obstacle_height: 0.0, data_type: LaserScan, topic: /virtual_sensor_scan, marking: true, clearing: false}
```

Cartorgrapher:
---------

```
roslaunch test_slam cartorgrapher.launch
```

Navigation:
------------

I prepare three projects for navigation:

- mrobot:
```
roslaunch test_slam navigate_mrobot.launch
```

- turtlebot3:
```
roslaunch test_slam navigate_turtlebot3.launch
```

- husky:
```
roslaunch test_slam navigate_husky.launch
```
