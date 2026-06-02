# ROS 2 Camera Action Graph

![ROS 2 camera action graph in Isaac Sim](ros2_camera_graph.png)

*Figure 1. Action graph used to publish camera data from Isaac Sim to ROS 2.*

## Purpose

This action graph publishes camera output from Isaac Sim to ROS 2. The graph advances the simulation, creates a render product from the selected camera prim, and passes that render product to the ROS 2 Camera Helper so image data can be published on a ROS 2 topic.

## Graph Setup

- Create a new Action Graph node and rename it to `ROS_Camera`.
- Add the nodes shown in the figure and connect them as displayed.
- Select the Isaac Sim camera prim that should be used as the image source.
- Configure the image resolution, topic name, and camera helper type for the ROS 2 stream you want to publish.

## Node Overview

- **On Playback Tick**: Triggers the graph while the Isaac Sim timeline is playing.
- **Isaac Run One Simulation Frame**: Steps the simulation once for each playback tick before camera data is generated.
- **Isaac Create Render Product**: Creates a render product from the selected camera prim. This render product is the source consumed by the camera helper.
- **ROS2 Context**: Creates the ROS 2 context required by the camera helper.
- **ROS2 Camera Helper**: Publishes the selected camera stream to ROS 2 using the render product path and ROS 2 context.

## Configuration Details

- Set **Isaac Create Render Product** input **Camera Prim** to the camera prim in the stage.
- Set **Isaac Create Render Product** inputs **Width** and **Height** to the desired image resolution.
- Set **ROS2 Camera Helper** input **Topic Name** to the ROS 2 image topic.
- Set **ROS2 Camera Helper** input **Frame Id** to the camera frame name used by the robot.
- Set **ROS2 Camera Helper** input **Type** to the camera output type you want to publish, such as `rgb`, `depth`, or another supported Isaac Sim camera helper stream.
- Enable **ROS2 Camera Helper** when the camera stream should be active.
- Enable **Semantic Labels** and set **Semantic Labels Topic Name** only when publishing semantic camera data.

## Connections Shown in the Figure

1. **On Playback Tick** connects its **Tick** output to the execution input of **Isaac Run One Simulation Frame**.
2. **Isaac Run One Simulation Frame** connects its **Step** output to the execution input of **Isaac Create Render Product**.
3. **Isaac Create Render Product** connects its **Exec Out** output to the execution input of **ROS2 Camera Helper**.
4. **ROS2 Context** connects its **Context** output to the **Context** input of **ROS2 Camera Helper**.
5. **Isaac Create Render Product** connects its **Render Product Path** output to the **Render Product Path** input of **ROS2 Camera Helper**.

## Result

With this setup, Isaac Sim publishes camera data through the ROS 2 bridge while the simulation timeline is running. External ROS 2 nodes can subscribe to the configured camera topic and consume the simulated camera stream for perception, visualization, or robot control workflows.
