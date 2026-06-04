# ROS 2 3D Lidar Point Cloud Action Graph

![ROS 2 3D Lidar point cloud action graph in Isaac Sim](3D-lidar-point-cloud-ag.png)

*Figure 1. Action graph used to publish 3D Lidar point cloud data from Isaac Sim to ROS 2.*

## Purpose

This action graph publishes a simulated 3D Lidar point cloud from Isaac Sim to ROS 2 as a `sensor_msgs/msg/PointCloud2` message. The graph is triggered while the Isaac Sim timeline is playing, reads a full point cloud scan from the selected Lidar prim, timestamps the data with simulation time, and sends the point buffer through the ROS 2 publisher node.

The graph shown here uses the PhysX/range-sensor Lidar workflow with **Isaac Read Lidar Point Cloud Node**. For newer RTX Lidar sensors, Isaac Sim also provides an RTX Lidar workflow based on **ROS2 RTX Lidar Helper** and optional metadata configuration nodes. Use the workflow that matches the sensor type in the stage.

## Graph Setup

- Create or open an Action Graph for the 3D Lidar pipeline.
- Add the nodes shown in the figure and connect them as displayed.
- Set **Isaac Read Lidar Point Cloud Node** input **Lidar Prim** to the Lidar prim in the USD stage.
- Configure the ROS 2 topic, frame ID, QoS, namespace, and queue size on **ROS2 Publish Point Cloud**.
- Make sure the ROS 2 bridge extension is enabled and Isaac Sim was started from a terminal where the target ROS 2 environment is sourced.

## Node Overview

- **On Playback Tick**: Triggers the graph while the Isaac Sim timeline is playing.
- **ROS2 Context**: Creates the ROS 2 context used by the publisher. It can use the default domain or a configured ROS domain ID.
- **ROS2 QoS Profile**: Builds the QoS profile passed into the point cloud publisher.
- **Isaac Read Lidar Point Cloud Node**: Reads point cloud data from the selected Lidar prim and outputs a 3D point buffer after a full scan is available.
- **Isaac Read Simulation Time**: Reads Isaac Sim simulation time so the ROS 2 message header can use simulator time instead of wall-clock time.
- **ROS2 Publish Point Cloud**: Publishes the point data as a ROS 2 `PointCloud2` message.

## Configuration Details

- Set **Lidar Prim** to the exact USD prim path of the 3D Lidar sensor.
- Set **Topic Name** to the ROS 2 point cloud topic, for example `/point_cloud` or `/lidar/points`.
- Set **Frame Id** to the coordinate frame of the Lidar, for example `sim_lidar`, `base_scan`, or the frame used by your robot model.
- Set **Node Namespace** if the robot runs under a namespace. The namespace is prepended to the published topic.
- Set **Queue Size** according to the expected publish rate and subscriber load. A small queue keeps latency low; a larger queue can tolerate short subscriber delays.
- Connect **ROS2 QoS Profile** when you need explicit reliability, durability, history, depth, lifespan, or liveliness behavior.
- Connect **Isaac Read Simulation Time** to **Timestamp** so downstream ROS 2 nodes can synchronize data with `/clock`.
- If RViz2 does not show the point cloud, check that RViz2 **Fixed Frame** matches the Lidar frame or that a valid TF transform exists from the fixed frame to the Lidar frame.

## Connections Shown in the Figure

1. **On Playback Tick** connects its **Tick** output to the execution input of **Isaac Read Lidar Point Cloud Node**.
2. **Isaac Read Lidar Point Cloud Node** connects its **Exec Out** output to **ROS2 Publish Point Cloud** **Exec In** so the publisher runs when a scan is ready.
3. **Isaac Read Lidar Point Cloud Node** connects its **Data** output to **ROS2 Publish Point Cloud** **Data** input.
4. **ROS2 Context** connects its **Context** output to **ROS2 Publish Point Cloud** **Context** input.
5. **ROS2 QoS Profile** connects its **Qos Profile** output to **ROS2 Publish Point Cloud** **Qos Profile** input.
6. **Isaac Read Simulation Time** connects its **Simulation Time** output to **ROS2 Publish Point Cloud** **Timestamp** input.

## ROS 2 Message

The output topic uses `sensor_msgs/msg/PointCloud2`. This message stores a collection of points in a binary data buffer and includes a ROS header with timestamp and coordinate frame information. For unordered point clouds, the message usually has `height = 1` and `width = number_of_points`.

Typical fields for a simple 3D Lidar point cloud are:

- `x`, `y`, `z`: Cartesian point coordinates.
- `header.stamp`: The simulation timestamp from Isaac Sim.
- `header.frame_id`: The Lidar frame set in **Frame Id**.

If the pipeline uses RTX Lidar metadata, additional fields such as intensity, object ID, material ID, timestamp, channel ID, emitter ID, or velocity can be included through the RTX-specific point cloud configuration workflow.

## Verify in ROS 2

Start Isaac Sim from a terminal where ROS 2 is sourced, then press **Play** in Isaac Sim. In another sourced terminal:

```bash
ros2 topic list
ros2 topic info /point_cloud -v
ros2 topic hz /point_cloud
ros2 topic echo /point_cloud --once
```

To visualize in RViz2:

```bash
rviz2
```

In RViz2, add a **PointCloud2** display, set the topic to `/point_cloud`, and set **Fixed Frame** to the Lidar frame or another frame connected to it through TF.

## RTX Lidar Note

NVIDIA's current RTX Lidar ROS 2 tutorial uses RTX sensors and helper nodes to publish `LaserScan` and `PointCloud2` messages. In that workflow:

- A 3D RTX Lidar can be created from **Create > Sensors > RTX Lidar > NVIDIA > Example Rotary** or from a vendor-specific RTX Lidar profile.
- The ROS 2 graph can be generated through **Tools > Robotics > ROS 2 OmniGraphs > RTX Lidar**.
- `PointCloud2` publishing can occur every frame or after a full scan, depending on the **Publish Full Scan** setting in the RTX Lidar helper.
- Optional metadata can be exposed with **ROS2 RTX Lidar Point Cloud Config**, including intensity, object ID, material ID, hit normals, timestamps, and velocity.

Use the RTX workflow when the sensor prim is an RTX/`OmniLidar` sensor. Use the graph in this file when the stage uses the PhysX/range-sensor Lidar node shown in Figure 1.

## Troubleshooting

- **No ROS 2 topic appears**: Confirm the ROS 2 bridge extension is enabled and Isaac Sim was launched from a sourced ROS 2 terminal.
- **Topic exists but no points are published**: Confirm the timeline is playing, the Lidar prim path is set, and the sensor is completing scans.
- **RViz2 shows no cloud**: Check the RViz2 fixed frame, `Frame Id`, and TF tree. A missing transform commonly makes valid point cloud messages invisible.
- **Point cloud is delayed or drops frames**: Tune **Queue Size** and QoS. Prefer lower queue sizes for low latency and larger queues when subscribers occasionally fall behind.
- **Wrong ROS domain**: Set **ROS2 Context** domain ID or enable **Use Domain ID Env Var** consistently with `ROS_DOMAIN_ID`.

## References

- NVIDIA Isaac Sim docs: [Isaac Read Lidar Point Cloud Node](https://docs.isaacsim.omniverse.nvidia.com/4.5.0/py/source/extensions/isaacsim.sensors.physx/docs/ogn/OgnIsaacReadLidarPointCloud.html)
- NVIDIA Isaac Sim docs: [ROS2 Publish Point Cloud](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.ros2.nodes/docs/ogn/OgnROS2PublishPointCloud.html)
- NVIDIA Isaac Sim docs: [RTX Lidar Sensors ROS 2 tutorial](https://docs.isaacsim.omniverse.nvidia.com/latest/ros2_tutorials/tutorial_ros2_rtx_lidar.html)
- NVIDIA Isaac Sim docs: [ROS2 RTX Lidar Point Cloud Config](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.ros2.nodes/docs/ogn/OgnROS2RtxLidarPointCloudConfig.html)
- ROS 2 docs: [sensor_msgs/msg/PointCloud2](https://docs.ros.org/en/humble/p/sensor_msgs/msg/PointCloud2.html)
