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

### Running on the Vicon PC (Windows 11, WSL2)

DataStream is then read locally (no frames lost on the VPN, stamps taken at the
source) and the driver reaches KITT and the robots over the VPN with
CycloneDDS unicast peers.

1. `%UserProfile%\.wslconfig` on the Vicon PC:
   ```ini
   [wsl2]
   networkingMode=mirrored
   [experimental]
   hostAddressLoopback=true   # WSL may connect to Tracker at the PC's own 10.8.0.3
   ```
   With it, `pic4ser_vicon.yaml` (`host_name: "10.8.0.3"`) works unchanged:
   the DataStream connection stays on the PC.
2. Hyper-V firewall (admin PowerShell), DDS on domain 6 with participant
   indexes up to 50 uses UDP 8910-9011:
   ```powershell
   New-NetFirewallHyperVRule -Name ROS2-DDS -DisplayName "ROS 2 DDS" -Direction Inbound `
     -VMCreatorId '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' -Protocol UDP -LocalPorts 8900-9100
   ```
3. In WSL (Ubuntu 24.04, ROS 2 Jazzy, this workspace built):
   ```bash
   export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp ROS_DOMAIN_ID=6
   export CYCLONEDDS_URI=file://$(ros2 pkg prefix mocap4r2_vicon_driver)/share/mocap4r2_vicon_driver/config/cyclonedds_vicon_pc.xml
   ros2 launch mocap4r2_vicon_driver pic4ser_vicon.launch.yaml
   ```
4. Add `<Peer Address="10.8.0.3"/>` to the CycloneDDS profiles of KITT and
   10.8.0.2, and stop the driver on KITT (one driver per domain).
5. Clocks: WSL takes the Windows time; sync Windows to the same source as KITT
   and the robots (stamps are compared with their TF).

## Acknowledgements

MOCAP4ROS2 started as a Focused Technical Project of [ROSIN](https://www.rosin-project.eu/),
which received funding from the European Union’s Horizon 2020 research and innovation
programme under grant agreement no. 732287. Developed at the
[Intelligent Robotics Lab](https://intelligentroboticslab.gsyc.urjc.es/), Rey Juan Carlos University.
