# 05 - Driving the TurtleBot4

## Purpose
This document explains how to **drive the TurtleBot4 manually**, explore its **ROS 2 topics and actions**, and use **docking/undocking** functionality. All steps have been tested on ROS 2 Humble with CycloneDDS.

---

View ROS2 Topics: 

```
ros2 topic list
```

<img width="905" height="964" alt="Screenshot from 2025-09-27 15-53-56" src="https://github.com/user-attachments/assets/0a897222-89bc-4a4c-94d8-2b684b0aac55" />


View ROS2 Actions: 

```
ros2 action list
```

<img width="786" height="533" alt="Screenshot from 2025-09-27 15-52-53" src="https://github.com/user-attachments/assets/4e96f432-eddc-460b-a3f2-88487e2c5a2b" />


Control using Keyboard: 
```
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

<img width="731" height="596" alt="image" src="https://github.com/user-attachments/assets/d4a3345b-b447-420c-a3fb-e92305bfd1dd" />


Action to follow wall: 
```
ros2 action send_goal /wall_follow irobot_create_msgs/action/WallFollow "{follow_side: 1, max_runtime: {sec: 30, nanosec: 0}}"
```

To Dock:

```
ros2 action send_goal /dock irobot_create_msgs/action/Dock "{}"
```

<img width="654" height="641" alt="image" src="https://github.com/user-attachments/assets/0140239d-e8e8-48aa-9e21-5232fde85547" />


To Undock:

```
ros2 action send_goal /undock irobot_create_msgs/action/Undock "{}"
```

<img width="786" height="533" alt="image" src="https://github.com/user-attachments/assets/4481d3ec-2740-4647-bf16-b3bd335044e6" />


To Rotate in Place:

```
ros2 action send_goal /rotate_angle irobot_create_msgs/action/RotateAngle "{angle: 1.57, max_rotation_speed: 1.0}"
```
- Rotates relative to current heading (in radians).
- Example: 1.57 → rotate ~90° left.
- max_rotation_speed in rad/s.

<img width="813" height="749" alt="image" src="https://github.com/user-attachments/assets/aade81b2-c583-460b-a2ef-3890fc9ffbc5" />


To Drive Straight Distance:

```
ros2 action send_goal /drive_distance irobot_create_msgs/action/DriveDistance "{distance: 0.5, max_translation_speed: 0.15}"
```

- Drives forward 0.5 meters.
- Negative distance = backward.
- Speed capped by max_translation_speed (m/s).

<img width="813" height="749" alt="image" src="https://github.com/user-attachments/assets/ec69468d-9870-4f05-a906-672b392e3c97" />


To Drive Arc:

```
ros2 action send_goal /drive_arc irobot_create_msgs/action/DriveArc "{angle: 1.57, radius: 0.3, max_translation_speed: 0.15}"
```
- Drive along a circular arc.
- angle: radians of arc to travel (positive = left turn, negative = right).
- radius: meters (distance from robot center to arc center).
- Example above → 90° left arc, radius 0.3 m.

<img width="813" height="749" alt="image" src="https://github.com/user-attachments/assets/dc3df667-598d-4330-b919-2b9ebee60c4a" />


LED Animation:

```
ros2 action send_goal /led_animation irobot_create_msgs/action/LedAnimation "{
  animation_type: 1,
  lightring: {
    leds: [
      {red: 255, green: 0, blue: 0},
      {red: 255, green: 0, blue: 0},
      {red: 255, green: 0, blue: 0},
      {red: 255, green: 0, blue: 0},
      {red: 255, green: 0, blue: 0},
      {red: 255, green: 0, blue: 0}
    ]
  },
  max_runtime: {sec: 5, nanosec: 0}
}"

```
The goal requires:
- animation_type (1 = blink, 2 = spin)
- lightring.leds (array of 6 LedColor)
- max_runtime
Here’s an example that blinks the lights red for 5 seconds:

<img width="885" height="986" alt="image" src="https://github.com/user-attachments/assets/f40513cb-fa56-419f-9edd-f95b3ac61b55" />



For MultiColor Animation-

Yellow → {red: 255, green: 255, blue: 0}
Pink → {red: 255, green: 0, blue: 255}
Cyan → {red: 0, green: 255, blue: 255}

Here’s an example command that assigns these colors to the 6 LEDs (cycling through them twice):
```
ros2 action send_goal /led_animation irobot_create_msgs/action/LedAnimation "{
  animation_type: 1,
  lightring: {
    leds: [
      {red: 255, green: 255, blue: 0},   # yellow
      {red: 255, green: 0, blue: 255},   # pink
      {red: 0, green: 255, blue: 255},   # cyan
      {red: 255, green: 255, blue: 0},   # yellow
      {red: 255, green: 0, blue: 255},   # pink
      {red: 0, green: 255, blue: 255}    # cyan
    ]
  },
  max_runtime: {sec: 5, nanosec: 0}
}"

```

<img width="885" height="986" alt="image" src="https://github.com/user-attachments/assets/1e9cba91-607b-4d44-8b0b-e35c00252496" />


Audio Sequence

```
ros2 action send_goal /audio_note_sequence irobot_create_msgs/action/AudioNoteSequence "{
  iterations: 1,
  note_sequence: {
    notes: [
      {frequency: 440, max_runtime: {sec: 1, nanosec: 0}}
    ]
  }
}"
```
The goal requires:
- iterations (number of times to play)
- note_sequence.notes (array of notes, each with frequency + max_runtime)

<img width="813" height="749" alt="image" src="https://github.com/user-attachments/assets/55b9560d-61d5-423a-9bfb-3053966f1e80" />

