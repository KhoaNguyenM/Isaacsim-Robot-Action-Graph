# ROS 2 Clock Publisher Action Graph

![ROS 2 clock publisher action graph in Isaac Sim](ROS2_Clock.png)

*Figure 1. Action graph used to publish the ROS 2 clock from Isaac Sim.*

## Purpose

This action graph publishes the simulation clock from Isaac Sim to ROS 2. It is configured as an on-demand pipeline so the graph can follow simulation updates while remaining lightweight and easy to control.

## Graph Setup

- Create a new Action Graph node and rename it to `ROS_Clock`.
- Set `pipelineStage` to `pipelineStageOnDemand`.
- Add the nodes shown in the figure and connect them as displayed.

## Node Overview

- **On Physics Step**: Triggers the graph when Isaac Sim physics steps and runs the action graph.
- **ROS2 Context**: Creates the ROS 2 context required by the clock publisher.
- **ROS2 QoS Profile**: Defines the Quality of Service settings used by the ROS 2 clock publisher.
- **ROS2 Publish Clock**: Publishes the ROS 2 clock message.
- **Isaac Read Simulation Time**: Reads the simulation time from Isaac Sim for the clock timestamp.

## Configuration Details

- Check the **Reset on Stop** input of **Isaac Read Simulation Time** so simulation time resets when the simulation stops.

## Connections Shown in the Figure

1. **On Physics Step** connects to the execution input of **ROS2 Publish Clock** so the graph runs on each physics update.
2. **ROS2 Context** connects to the **Context** input of **ROS2 Publish Clock**.
3. **ROS2 QoS Profile** connects to the **Qos Profile** input of **ROS2 Publish Clock**.
4. **Isaac Read Simulation Time** connects its **Simulation Time** output to the **Time Stamp** input of **ROS2 Publish Clock**.

## Result

With this setup, Isaac Sim continuously publishes simulation time as a ROS 2 clock message, allowing other ROS 2 nodes to use the simulator time base during execution.