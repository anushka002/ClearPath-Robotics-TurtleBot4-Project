# 01 - Raspberry Pi Setup — Networking, SSH, and ROS2 installation (Detailed A→Z)

## Purpose
Record every command, file I created or edited, why I edited it, and the exact contents so someone can reproduce the Raspberry Pi setup exactly as I did.

## Short state snapshot (final status from this step)
- Raspberry Pi (TurtleBot4 compute): `wlan0` → `192.168.12.199/24`
- USB fallback interface: `usb0` → `192.168.186.3` (example observed)
- ROS2: `humble`; `ROS_DOMAIN_ID=0`; `RMW_IMPLEMENTATION=rmw_cyclonedds_cpp`
- Verified: `ros2 topic list` shows Create3 topics

---

## 1) Interfaces I observed (confirm with commands)
- `ip a` to list interfaces.
- Observed names:
  - `usb0` — USB network to host
  - `wlan0` — Wi-Fi radio
  - `eth0` — physical ethernet (unused)

Example command(s) I ran:
- `ip addr show`
- `ip -br addr`

---

## 2) Netplan — what I changed and why
### Background
The image ships with a netplan file created by `turtlebot4_setup` (used for initial AP / installer). I replaced / edited a netplan to set a **static client IP** for `wlan0` so the Pi is reachable reliably on our home Wi-Fi subnet.

### Edit performed
File edited: `/etc/netplan/50-wifis.yaml`

I opened it with:
- `sudo nano /etc/netplan/50-wifis.yaml`

**Final `50-wifis.yaml` used (exact contents):**
```
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      optional: true
      dhcp4: false
      addresses: [192.168.12.199/24]
      gateway4: 192.168.12.1
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
      access-points:
        "TurtleNet-Home":
          password: "REDACTED_WIFI_PASSWORD"
```

> Replace `TurtleNet-Home` and the password with your SSID and password. The `gateway4` value matches the local router/gateway for the `192.168.12.0/24` subnet.

Apply the config:
- `sudo netplan apply`

Verify:
- `ip addr show wlan0`
- `ping 192.168.12.112` (Create3)
- `ping google.com` (internet test)

### Why static IP?
I chose a static IP `192.168.12.199` to avoid the Pi receiving a different DHCP address — consistent SSH and ROS2 endpoint for the robot.

**Reference for netplan examples:** official netplan examples. :contentReference[oaicite:1]{index=1}

---

## 3) SSH access & initial troubleshooting
- SSH command used after static IP applied:
  - `ssh ubuntu@192.168.12.199` (password: the user-created password)
- Earlier (before static netplan) the image default AP/ssh address could be `10.42.0.1`. Example initial SSH:
  - `ssh ubuntu@10.42.0.1` (password: `turtlebot4`) — this is image-specific; I then reconfigured netplan.

**Unattended updates avoidance**
Before connecting to internet (to avoid automatic OS upgrades), I removed unattended-upgrades:

- `sudo apt remove unattended-upgrades`

If apt/dpkg is locked (common during background updates), you may see:
- `Waiting for cache lock: Could not get lock /var/lib/dpkg/lock-frontend...`

Action I did:
- Wait for the background process to finish OR temporarily stop it:
  - `sudo systemctl stop unattended-upgrades` (if running)
  - then `sudo apt remove unattended-upgrades`  
  - (If you still get lock errors, wait a few minutes or reboot and retry.)

Reference/discussion about unattended-upgrades removal behavior. :contentReference[oaicite:2]{index=2}

---

## 4) ROS2 Humble — exact install steps I executed on the Pi
**Commands executed (exact):**
1. Ensure universe repo and basic tools:
   - `sudo apt update && sudo apt install -y software-properties-common`
   - `sudo add-apt-repository universe`
2. Install curl and add repo key:
   - `sudo apt install -y curl`
   - `sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg`
3. Add ROS2 apt source:
   - `echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null`
4. Update packages:
   - `sudo apt update && sudo apt upgrade -y`
5. Install ROS2 (desktop GUI) or base:
   - If GUI: `sudo apt install -y ros-humble-desktop`
   - If headless (minimal): `sudo apt install -y ros-humble-ros-base`
6. Install Create3 messages & recommended extras:
   - `sudo apt install -y ros-humble-irobot-create-msgs`
   - `sudo apt install -y build-essential python3-colcon-common-extensions python3-rosdep ros-humble-rmw-cyclonedds-cpp`

7. Add auto-source to `~/.bashrc`:
   - `echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc`
   - `echo "source /etc/turtlebot4/setup.bash" >> ~/.bashrc`  (this sources TurtleBot4-specific environment)
   - `echo "export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp" >> ~/.bashrc`
   - `echo "export ROS_DOMAIN_ID=0" >> ~/.bashrc`

8. Reload shell:
   - `source ~/.bashrc`

These instructions follow the Create3/Humble guide I used as the baseline. :contentReference[oaicite:3]{index=3}

---

## 5) CycloneDDS / RMW on the Pi
I selected CycloneDDS as the RMW to match the Create3 setting (`rmw_cyclonedds_cpp`).

Set environment variables (persisted in `~/.bashrc`):
- `export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp`
- `export ROS_DOMAIN_ID=0`

I also created a CycloneDDS XML profile (see `04-UserPC-Setup` for the PC copy). On Pi this was **not strictly required** because Create3 Ib UI controls the robot's RMW and uses its own profile, but exporting `RMW_IMPLEMENTATION` ensures Pi-side processes use Cyclone.

**Reference:** Create3 middleware & CycloneDDS guidance. :contentReference[oaicite:4]{index=4}

---

## 6) Verification (quick commands I ran)
- `ssh ubuntu@192.168.12.199`
- `printenv ROS_DISTRO`
- `ros2 topic list`
- `ip addr show wlan0`
- `ping 192.168.12.112` (Create3)
- `ping google.com` (internet)

If `ros2 topic list` doesn't show robot topics, check `RMW_IMPLEMENTATION` and Cyclone DDS xml (see `04-UserPC-Setup`).

---

## Final notes / next steps
- Pi is reachable at `192.168.12.199`.
- `ros2` is installed and basic Create3 messages installed.
- Next: configure Create3 via Ib UI (see `02-Create3-Setup`), and configure PC CycloneDDS to use `usb0,wlan0` interfaces and `DontRoute` as needed (see `04-UserPC-Setup`).
