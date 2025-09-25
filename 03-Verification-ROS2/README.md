# 03 - Final Verification — Confirming ROS2 topics & end-to-end connectivity

## Purpose
Commands to confirm the entire system is connected and the topics required for demos are visible.

## Quick checklist (do these in order)
1. SSH to Pi: `ssh ubuntu@192.168.12.199`
2. Source the environment:
   - `source /opt/ros/humble/setup.bash`
   - `source /etc/turtlebot4/setup.bash`
3. Confirm ROS distro:
   - `printenv ROS_DISTRO`  (should output `humble`)

---

## Verify topics (commands I ran)
- `ros2 topic list`  — confirms discovery and lists active topics.

**Topics I observed (example list captured during verification):**
- `/rpi_anushka/imu`
- `/rpi_anushka/oakd/rgb/preview/image_raw`
- `/rpi_anushka/cmd_vel`
- `/rpi_anushka/battery_state`

(These topics confirm IMU, camera, motion command, and battery telemetry are reachable.)

To inspect a live message example:
- IMU: `ros2 topic echo /rpi_anushka/imu --once`
- Camera preview (metadata): `ros2 topic echo /rpi_anushka/oakd/rgb/preview/image_raw --qos-profile sensor_data` (use appropriate QoS for large data streams)

---

## Network checks (commands)
- `ping 192.168.12.199` (Pi) — verifies SSH reachable
- `ping 192.168.12.112` (Create3) — verifies base reachable
- `ping google.com` — verifies internet access (if gateway configured)

---

## Troubleshooting common failures
1. `ros2 topic list` shows nothing:
   - Confirm `RMW_IMPLEMENTATION` is the same on PC, Pi, and Create3:
     - `echo $RMW_IMPLEMENTATION`
   - Confirm `ROS_DOMAIN_ID` matches:
     - `echo $ROS_DOMAIN_ID`
   - If using CycloneDDS with multiple interfaces, ensure the PC's Cyclone config contains the interfaces or `DontRoute=true`. See `04-UserPC-Setup` CycloneDDS XML examples. :contentReference[oaicite:7]{index=7}

2. `ssh` fails:
   - Verify `ip addr show wlan0` on Pi.
   - Verify `sudo netplan apply` succeeded on Pi.

3. Intermittent discovery:
   - Restart ROS processes after network changes.
   - Try `ros2 daemon stop` and `ros2 daemon start` on the machine where the CLI is run.

---

## Status (this test)
- System passes verification: Pi and Create3 topics visible to `ros2` CLI, network connectivity verified.
