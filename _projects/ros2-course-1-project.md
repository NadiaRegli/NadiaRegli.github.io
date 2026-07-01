### Step 3 - Turtle Controller Node

The `turtle_controller` node drives the master turtle (`turtle1`) towards target turtles:

- Subscribes to `/turtle1/pose` for the current position and heading.
- Publishes velocity commands to `/turtle1/cmd_vel`.
- Subscribes to `/alive_turtles` to get available targets.
- Uses a proportional (P) controller to compute linear and angular velocity:
  - Distance to target determines linear speed
  - Angle difference determines angular speed (using `atan2` for correct heading)
- When a turtle is reached, calls the `/catch_turtle` service.
- Configurable parameter `catch_closest_turtle_first` to select the nearest target instead of the first in the list.

### Step 4 - Turtle Spawner Node

The `turtle_spawner` node manages the lifecycle of turtles on screen:

- Timer-based spawning: periodically calls the `/spawn` service with random coordinates (0.0-11.0).
- Maintains an array of alive turtles with names and coordinates.
- Publishes the alive turtles list on `/alive_turtles`.
- Service server (`/catch_turtle`): when called, kills the specified turtle via the `/kill` service and removes it from the alive list.
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

- Nodes & multi-node architecture: designing a system with clear separation of concerns
- Topics (pub/sub): real-time data streaming between nodes
- Services (client/server): request/response communication for discrete actions
- Custom interfaces: defining `.msg` and `.srv` types for domain-specific data

</div>
<div class="col-md-6" markdown="1">

#### Robotics & Software Engineering

- Proportional (P) control: implementing a control loop for autonomous navigation
- Coordinate geometry: computing distances and angles with `atan2`
- Parameters & launch files: making the system configurable and scalable
- System design: planning node communication before writing code
- ROS 2 build system: `colcon`, `setup.py`, `package.xml` dependencies

</div>
</div>

---

## Technologies Used

`ROS 2` · `Python` · `Turtlesim` · `Linux (Ubuntu)` · `rqt_graph` · `colcon` · `ament_python` · `Custom Messages & Services` · `Launch Files` · `YAML Parameters`
