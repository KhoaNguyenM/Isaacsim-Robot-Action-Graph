# ROS 2 Joint State Action Graph

![ROS 2 Joint State action graph in Isaac Sim](ROS2_JointState.png)

*Figure 1. Action graph used to publish joint states to ROS 2 and receive joint state commands in Isaac Sim.*

## Purpose

This action graph publishes the current joint states from Isaac Sim to ROS 2 and subscribes to incoming joint state commands. It is configured as an on-demand pipeline so the graph can run only when needed, while still being synchronized with the simulation physics step.

## Graph Setup

- Create a new Action Graph node and rename it to `ROS_Joint_States`.
- Set `pipelineStage` to `pipelineStageOnDemand`.
- Add the nodes shown in the figure and connect them as displayed.

## Node Overview

- **On Physics Step**: Triggers the graph on every Isaac Sim physics step so the graph stays aligned with simulation updates.
- **ROS2 Context**: Creates the ROS 2 context required by the publisher and subscriber nodes.
- **ROS2 QoS Profile**: Defines the Quality of Service settings used by the ROS 2 joint state communication.
- **ROS2 Subscribe Joint State**: Subscribes to joint state commands from the external policy node.
- **ROS2 Publish Joint State**: Publishes the current joint states from Isaac Sim to ROS 2.
- **Isaac Read Simulation Time**: Reads simulation time so the published joint state message can be timestamped correctly.
- **Articulation Controller**: Applies the subscribed joint commands to the robot articulation in Isaac Sim.

## Configuration Details

- Set **ROS2 Publish Joint State** input **Target Prim** to `/h1`.
- Set **ROS2 Publish Joint State** input **Topic Name** to `/joint_states`.
- Set **ROS2 Subscribe Joint State** input **Topic Name** to `/joint_command`.
- Set **Articulation Controller** input **Target Prim** to `/h1`.
- Check the **Reset on Stop** input of **Isaac Read Simulation Time** so simulation time resets when the simulation stops.

## Connections Shown in the Figure

1. **On Physics Step** connects to the execution inputs of the ROS 2 publisher, subscriber, and articulation controller nodes so the graph runs during physics updates.
2. **ROS2 Context** connects to the **Context** input of both **ROS2 Publish Joint State** and **ROS2 Subscribe Joint State**.
3. **ROS2 QoS Profile** connects to the **Qos Profile** input of both **ROS2 Publish Joint State** and **ROS2 Subscribe Joint State**.
4. **Isaac Read Simulation Time** connects its **Simulation Time** output to the **Timestamp** input of **ROS2 Publish Joint State**.
5. **ROS2 Subscribe Joint State** provides the incoming command outputs that drive **Articulation Controller**:
   - Effort Command
   - Joint Names
   - Position Command
   - Velocity Command

## Result

With this setup, Isaac Sim can publish robot joint states to ROS 2 while also receiving joint commands from an external policy or controller node. The articulation controller then applies those commands to the robot model inside the simulation.