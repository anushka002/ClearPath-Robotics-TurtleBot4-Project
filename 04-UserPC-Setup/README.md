# 04 - User PC Setup — workstation configuration (exact commands, cyclonedds, and final checks)

## Purpose
This file records exactly what we configured on the user PC (Ubuntu dual-boot) so it can see the TurtleBot4 topics reliably across `usb0` and `wlan0`.

## Short state snapshot (as of this log)
- PC interface `wlo1` → `192.168.12.163`
- PC uses `rmw_cyclonedds_cpp`
- CYCLONEDDS profile created to use `usb0` & `wlan0` and `DontRoute` when needed

---

## 1) ROS2 and basic packages (commands we executed)
Follow the same ROS2 Humble steps as on the Pi (see `01-RaspberryPi-Setup`). Key commands we used:
- `sudo apt update && sudo apt install -y curl`
- `sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg`
- `echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null`
- `sudo apt update && sudo apt upgrade -y`
- `sudo apt install -y ros-humble-desktop ros-humble-irobot-create-msgs ros-humble-rmw-cyclonedds-cpp`
- `echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc`
- `echo "export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp" >> ~/.bashrc`
- `echo "export ROS_DOMAIN_ID=0" >> ~/.bashrc`
- `source ~/.bashrc`

Source: Create3 / ROS2 install guidance. :contentReference[oaicite:8]{index=8}

---

## 2) CycloneDDS XML + env setup (exact file + why)
### Why
When robots and compute boards use multiple network interfaces (here `usb0` and `wlan0`), CycloneDDS can be configured to listen on only those interfaces. This avoids discovery problems and works well in our Pi + Create3 + PC setup.

### File we created (exact path)

On the User PC we created the file:  **/etc/turtlebot4/cyclonedds_pc.xml**

```
<CycloneDDS>
  <Domain>
    <General>
      <NetworkInterfaceAddress>wlo1</NetworkInterfaceAddress>
      <AllowMulticast>true</AllowMulticast>
    </General>
    <Discovery>
      <ParticipantIndex>auto</ParticipantIndex>
    </Discovery>
  </Domain>
</CycloneDDS>
```

**Why these choices?**  
- `NetworkInterfaceAddress=wlo1` ensures DDS binds to the Wi-Fi card (subnet `192.168.12.0/24`).  
- Multicast enabled so automatic peer discovery works with Pi + Create3.  
- No static peers needed — all devices are on the same subnet.  

---

## 2) Environment setup
We sourced the following environment file:  

**/etc/turtlebot4/setup.bash**

```
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export ROS_DOMAIN_ID=0
export CYCLONEDDS_URI=/etc/turtlebot4/cyclonedds_pc.xml
```

Set environment variable to load the config
- `export CYCLONEDDS_URI=file:///home/<your_user>/cyclonedds/cyclonedds.xml`
- To persist: `echo "export CYCLONEDDS_URI=file:///home/<your_user>/cyclonedds/cyclonedds.xml" >> ~/.bashrc`

**Important**: the interfaces specified must be active when a ROS 2 process starts. See CycloneDDS + Create3 docs for examples. :contentReference[oaicite:9]{index=9}

---

## 3) `DontRoute` note (why we used this)
- In some CycloneDDS versions (0.8.1) on laptops, discovery over multiple interfaces requires `DontRoute=true` so multicast/discovery packets are not incorrectly routed. This was recommended in Create3 docs and solved intermittent topic visibility for us. :contentReference[oaicite:10]{index=10}

---

## 4) Final verification on PC
1. Confirm interface IP:
   - `ip addr show wlo1`  (should show `192.168.12.163`)
2. Source env:
   - `source ~/.bashrc`
3. Run:
   - `ros2 topic list`  — should display topics like:
     - `/rpi_anushka/imu`
     - `/rpi_anushka/oakd/rgb/preview/image_raw`
     - `/rpi_anushka/cmd_vel`
     - `/rpi_anushka/battery_state`
4. If topics are not visible:
   - Confirm `echo $RMW_IMPLEMENTATION` → `rmw_cyclonedds_cpp`
   - Confirm `echo $ROS_DOMAIN_ID` → `0`
   - Confirm `echo $CYCLONEDDS_URI` points to your cyclonedds.xml
   - Restart any running ROS2 processes after net/config changes.

---

## 5) Troubleshooting notes we encountered & fixes
- Symptom: `ros2 topic list` empty on laptop though Pi shows topics.
  - Fix: exported `CYCLONEDDS_URI` pointing to `cyclonedds.xml` restricting interfaces to `usb0,wlan0` and adding `DontRoute=true`. Restarted the shell and CLI. That resolved discovery issues.
- Symptom: `ssh` to Pi works but Create3 web UI not directly reachable at `192.168.12.112:8080`.
  - Fix: use Pi proxy `http://192.168.12.199:8080`.

---

## References
- Create3 ROS 2 / middleware guidance (Cyclone examples and DontRoute recommendation). :contentReference[oaicite:11]{index=11}
- ROS 2 Humble install steps. :contentReference[oaicite:12]{index=12}
