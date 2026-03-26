---
layout: page
title: ROS2 Turtlesim - Catch Them All
description: A complete ROS2 project using Turtlesim to implement multi-node communication, control loops, custom interfaces, and launch files.
img: assets/img/ros2-course-1/thumbnail.png
importance: 1
category: mechatronics
---

## Overview

A complete robotics project built with **ROS 2** using the **Turtlesim** simulation package in a Linux (Ubuntu) environment. The goal: a "master" turtle autonomously chases and catches randomly spawning turtles on screen - demonstrating core ROS 2 concepts in a practical, visual application.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/ros2-course-1-final-project-demo.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true muted=true %}
    </div>
</div>

<div class="caption">
    Demo of the final result and implemenation walkthrough - the master turtle (turtle1) autonomously navigates to and catches each spawned turtle.
</div>

---

## Architecture

The system is composed of **3 nodes** that communicate via topics and services:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ros2-course-1/architecture.png" title="RQT Graph Final Result" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    RQT graph of the final result - showing the three nodes (/turtle_controller, /turtle_spawner, /turtlesim) and topics (/alive_turtles, /turtle1/cmd_vel, /turtle1/pose) used.
</div>

---

### Custom Interfaces

Custom message and service types were created in the `my_robot_interfaces` package:

| Interface         | Type    | Purpose                                                     |
| ----------------- | ------- | ----------------------------------------------------------- |
| `Turtle.msg`      | Message | Holds a single turtle's name, x, and y coordinates          |
| `TurtleArray.msg` | Message | Array of `Turtle` messages for the `/alive_turtles` topic   |
| `CatchTurtle.srv` | Service | Sends the name of a caught turtle from controller → spawner |

---

## Implementation

### Step 1 - Package Setup

Created a new ROS 2 Python package:

```bash
ros2 pkg create --build-type ament_python turtlesim_catch_them_all
```

### Step 2 - Custom Interfaces

Defined `Turtle.msg`, `TurtleArray.msg`, and `CatchTurtle.srv` inside `my_robot_interfaces`:

```
# Turtle.msg          # TurtleArray.msg         # CatchTurtle.srv
string name            Turtle[] turtles           string name
float64 x                                         ---
float64 y                                         bool success
```

### Step 3 - Turtle Controller Node

The `turtle_controller` node drives the master turtle (`turtle1`) towards target turtles:

- **Subscribes** to `/turtle1/pose` for the current position and heading.
- **Publishes** velocity commands to `/turtle1/cmd_vel`.
- **Subscribes** to `/alive_turtles` to get available targets.
- Uses a **proportional (P) controller** to compute linear and angular velocity:
  - Distance to target → linear speed
  - Angle difference → angular speed (using `atan2` for correct heading)
- When a turtle is reached, calls the `/catch_turtle` service.
- Configurable parameter `catch_closest_turtle_first` to select the nearest target instead of the first in the list.

### Step 4 - Turtle Spawner Node

The `turtle_spawner` node manages the lifecycle of turtles on screen:

- **Timer-based spawning**: periodically calls the `/spawn` service with random coordinates (0.0–11.0).
- Maintains an **array of alive turtles** with names and coordinates.
- **Publishes** the alive turtles list on `/alive_turtles`.
- **Service server** (`/catch_turtle`): when called, kills the specified turtle via the `/kill` service and removes it from the alive list.
- Configurable parameters: `spawn_frequency` and `turtle_name_prefix`.

### Step 5 - Parameters & Launch File

Created a YAML parameter file and an XML launch file in `my_robot_bringup` to launch all 3 nodes with configurable parameters:

```yaml
# turtle_catcher_params.yaml
turtle_controller:
  ros__parameters:
    catch_closest_turtle_first: true

turtle_spawner:
  ros__parameters:
    spawn_frequency: 1.0
    turtle_name_prefix: "turtle"
```

```xml
<!-- turtle_catcher.launch.xml -->
<launch>
    <node pkg="turtlesim" exec="turtlesim_node" />
    <node pkg="turtlesim_catch_them_all" exec="turtle_controller"
          params="turtle_catcher_params.yaml" />
    <node pkg="turtlesim_catch_them_all" exec="turtle_spawner"
          params="turtle_catcher_params.yaml" />
</launch>
```

Run with a single command:

```bash
ros2 launch my_robot_bringup turtle_catcher.launch.xml
```

---

## Key Skills & Learnings

<div class="row" markdown="1">
<div class="col-md-6" markdown="1">

#### ROS 2 Core Concepts

- **Nodes & multi-node architecture** — designing a system with clear separation of concerns
- **Topics** (pub/sub) — real-time data streaming between nodes
- **Services** (client/server) — request/response communication for discrete actions
- **Custom interfaces** — defining `.msg` and `.srv` types for domain-specific data

</div>
<div class="col-md-6" markdown="1">

#### Robotics & Software Engineering

- **Proportional (P) control** — implementing a control loop for autonomous navigation
- **Coordinate geometry** — computing distances and angles with `atan2`
- **Parameters & launch files** — making the system configurable and scalable
- **System design** — planning node communication before writing code
- **ROS 2 build system** — `colcon`, `setup.py`, `package.xml` dependencies

</div>
</div>

---

## Technologies Used

`ROS 2` · `Python` · `Turtlesim` · `Linux (Ubuntu)` · `rqt_graph` · `colcon` · `ament_python` · `Custom Messages & Services` · `Launch Files` · `YAML Parameters`
