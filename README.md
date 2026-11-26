# MOCAP4ROS2 Vicon driver

[![rolling](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/rolling.yaml/badge.svg?branch=rolling)](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/rolling.yaml) [![lyrical](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/lyrical.yaml/badge.svg?branch=rolling)](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/lyrical.yaml) [![kilted](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/kilted.yaml/badge.svg?branch=rolling)](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/kilted.yaml) [![jazzy](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/jazzy.yaml/badge.svg?branch=rolling)](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/jazzy.yaml) [![humble](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/humble.yaml/badge.svg?branch=rolling)](https://github.com/MOCAP4ROS2-Project/mocap4ros2_vicon/actions/workflows/humble.yaml)

`mocap4r2_vicon_driver` streams Vicon Nexus, Tracker and Shogun into
[MOCAP4ROS2](https://mocap4ros2-project.github.io). It builds the Vicon DataStream SDK
1.13 from its sources (MIT license, in `ThirdParty/`); no Boost, no binaries.

Documentation: https://mocap4ros2-project.github.io/latest/systems/vicon.html

## PIC4SeR fork (branch `port-2.0`)

The upstream 2.0.0 driver, unmodified, plus the lab configuration:

```bash
ros2 launch mocap4r2_vicon_driver pic4ser_vicon.launch.yaml            # streams on /rigid_bodies
ros2 launch mocap4r2_vicon_driver pic4ser_vicon.launch.yaml rviz:=true
```

* `config/pic4ser_vicon.yaml`: lab host and `frame_id: mocap` (from the pre-2.0 config).
* `launch/pic4ser_vicon.launch.yaml`: `autostart:=activate` by default, like the
  pre-2.0 launch; RViz off by default.

Changes from the pre-2.0 driver that affect consumers: messages are
`mocap4r2_interfaces` (was `mocap4r2_msgs`); a rigid body is named by its subject
(`Jackal`, was `Jackal.Jackal`); an occluded subject is published with
`tracking_valid: false` and an identity pose (was an all-zero pose); stamps are
compensated for the Vicon latency; the node is `vicon_driver` (was
`mocap4r2_vicon_driver_node`).

## Acknowledgements

MOCAP4ROS2 started as a Focused Technical Project of [ROSIN](https://www.rosin-project.eu/),
which received funding from the European Union’s Horizon 2020 research and innovation
programme under grant agreement no. 732287. Developed at the
[Intelligent Robotics Lab](https://intelligentroboticslab.gsyc.urjc.es/), Rey Juan Carlos University.
