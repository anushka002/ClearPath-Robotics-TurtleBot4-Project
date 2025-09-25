# 02 - Create3 (iRobot) Setup — Web UI configuration & RMW settings

## Purpose
Record every setting we applied to the iRobot Create3 via its web UI, why we applied it, and how it interacts with the Pi and PC.

---

## Short snapshot
- Create3 IP (on Wi-Fi client): `192.168.12.112`
- Create3 fallback AP IP: `192.168.10.1` (factory mode)
- Web UI accessed through Pi proxy: `http://192.168.12.199:8080` (proxy from Pi)
- Firmware: `H.2.6` (observed)
- RMW chosen: `rmw_cyclonedds_cpp`
- Namespace: `<empty>`
- ROS_DOMAIN_ID: `0`
- Param override: `safety_override="backup_only"`

<img width="1920" height="995" alt="image" src="https://github.com/user-attachments/assets/4d940b55-2d98-4db0-a5b4-29fdce60b3cf" />


---

## 1) Accessing Create3 web UI via the Pi proxy
We used the Pi as a proxy to reach the Create3 web UI (the Pi runs a small HTTP proxy by default in TurtleBot images). Access:

- `http://192.168.12.199:8080`  (the Pi’s proxy forwarding to Create3)

If you cannot reach the Create3 UI directly at `192.168.12.112:8080`, the proxy is the most reliable option.

---

## 2) Exact settings we applied (Application → Configuration)
Open: `Application` → `Configuration` in Create3 Web UI, then set:

- `ROS_DOMAIN_ID`: `0`
- `Namespace`: `<empty>` (default, topics like `/odom`, `/cmd_vel` appear without prefix)
- `RMW_IMPLEMENTATION`: `rmw_cyclonedds_cpp`
- `Fast DDS Discovery Server`: **disabled** for this simple Wi-Fi client setup

### Motion Control Safety Override
We added a parameter override for safe reversing:  

```yaml
c3_anushka:
  motion_control:
    ros__parameters:
      # Safety override modes:
      # "none"         – standard safety profile
      # "backup_only"  – allow reverse motion without rear cliff sensors
      # "full"         – disable cliff safety entirely
      safety_override: "backup_only"
