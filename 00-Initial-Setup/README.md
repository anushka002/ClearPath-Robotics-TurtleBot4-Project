# 00 - Initial Setup — SD card flashing & first boot (Authoritative log)

## Purpose
This file records the exact steps we took to format and flash the TurtleBot4 Raspberry Pi SD card, plus the verification checks we performed. This is the canonical "first milestone" log you should keep.

## Short state snapshot (from this step)
- Flashed image: `turtlebot4_humble_lite_1.0.4` (note: `1.0.5` failed while flashing; we used `1.0.4`)
- Host machine used: Windows (Balena Etcher)
- Post-flash: Pi booted and reached login prompt

---

## 1) Download + verify the image
1. Downloaded the image from TurtleBot4 site:
   - URL used (example): `http://download.ros.org/downloads/turtlebot4/turtlebot4_lite_humble_1.0.4.zip`
2. Unzipped to obtain `turtlebot4_lite_humble_1.0.4.img`
3. Verified checksum (example command run on Linux/WSL / Windows Linux shell):
   - `sha256sum turtlebot4_lite_humble_1.0.4.img`
   - Compare the printed value with the checksum provided on the download page.

---

## 2) Flashing (Windows — Balena Etcher)
1. Insert SD card (8GB+ recommended; 16GB preferred).
2. Start Balena Etcher: Visit [Link to Download](http://download.ros.org/downloads/turtlebot4/) - select image `turtlebot4_lite_humble_1.0.4.img`, select target SD card drive, click `Flash`.
3. Wait until flashing completes. Note: Etcher may warn that SD card is larger than image — that is normal.
4. Safely eject SD card.

**Troubleshooting note:** `turtlebot4_humble_lite_1.0.5` failed on this machine. We re-downloaded `1.0.4` and it worked.

---

## 3) First boot on Raspberry Pi (one-time GUI/keyboard steps)
1. Insert SD into Raspberry Pi.
2. Connect temporary HDMI + keyboard (optional, helpful for first-run).
3. Connect USB power to Pi and power on.
4. During first-boot:
   - Create the `ubuntu` user and set password.
   - Enable SSH (or keep default enabled per image).
5. Confirm you can SSH (AP default or initial IP may be `10.42.0.1` depending on image).

**Important:** the stock image may set an initial AP / default Wi-Fi/SSH state. We later overwrite with our static netplan (see `01-RaspberryPi-Setup`).

---

## 4) Helpful commands used / notes
- If using a Linux shell to verify the image: `sha256sum <img>`  
- On Windows, confirm drive letter carefully in Etcher before flashing (to avoid overwriting other disks).

---

## 5) Source references / where we followed official guidance
- TurtleBot4 setup scripts and docs (install + netplan recommendations).
- [Turtlebot4 Setup Guide](https://turtlebot.github.io/turtlebot4-user-manual/software/turtlebot4_setup.html) 

---

## Status (end of this step)
- SD card formatted & `turtlebot4_lite_humble_1.0.4` flashed successfully.
- Raspberry Pi booted and is ready for network configuration (move to `01-RaspberryPi-Setup`).
