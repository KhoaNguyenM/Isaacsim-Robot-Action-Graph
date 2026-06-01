# IMU Action Graph for ROS 2 Publishing

![IMU action graph in Isaac Sim](IMU_action_graph.png)

*Figure 1. Action graph used to publish IMU data from Isaac Sim to ROS 2.*

## Purpose

This action graph publishes IMU data from Isaac Sim to ROS 2. The graph is triggered every physics step, reads IMU data from the pelvis-mounted sensor, and forwards the selected values to a ROS 2 IMU topic.

## Node Overview

- **On Physics Step**: Triggers the graph on every Isaac Sim physics step so the data stream stays synchronized with the simulation.
- **ROS2 Context**: Creates the ROS 2 context required by the publisher.
- **ROS2 QoS Profile**: Defines the Quality of Service settings used by the ROS 2 IMU publisher.
- **Isaac Read IMU Node**: Reads the IMU sensor values from Isaac Sim.
- **Isaac Read Simulation Time**: Reads simulation time to generate the message timestamp.
- **ROS2 Publish IMU**: Publishes the IMU message to ROS 2 using the outputs from the IMU and simulation time nodes.

## Connections Shown in the Figure

1. **On Physics Step** connects to the execution input of **Isaac Read IMU Node**.
2. **ROS2 Context** connects to the **Context** input of **ROS2 Publish IMU**.
3. **ROS2 QoS Profile** connects to the **Qos Profile** input of **ROS2 Publish IMU**.
4. **Isaac Read IMU Node** outputs:
   - Angular Velocity Vector
   - Linear Acceleration Vector
   - Sensor Orientation Quaternion
   - Sensor Time
   These outputs feed the matching inputs on **ROS2 Publish IMU**.
5. **Isaac Read Simulation Time** connects its **Simulation Time** output to the **Timestamp** input of **ROS2 Publish IMU**.
