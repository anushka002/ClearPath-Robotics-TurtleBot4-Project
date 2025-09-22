# ClearPath-Robotics-TurtleBot4-Project


# Initial Setup — SD Card Preparation & First Boot

This document explains how to prepare the SD card and perform the very first boot of the Raspberry Pi inside the TurtleBot4.

---

## 1. Getting the OS Image
- Download **Ubuntu 22.04 Server (arm64)** for Raspberry Pi (compatible with ROS2 Humble).
- Verify the checksum to ensure the image is correct.

## 2. Flashing the SD Card
- Use [Balena Etcher](https://etcher.balena.io/) (Windows) or `Gnome Disks` (Linux).
- Select the SD card drive carefully to avoid overwriting other storage.
- Flash the `.img` file to the SD card. Download the image file from the given [link](http://download.ros.org/downloads/turtlebot4/).

## 3. First Boot on Raspberry Pi
- Insert the SD card into the Raspberry Pi.
- Connect to USB power, and temporarily attach an HDMI monitor + keyboard.
- Complete the first boot setup:
  - Create the `ubuntu` user.
  - Set a password.
  - Enable SSH for remote access.

✅ Raspberry Pi is now ready for networking and ROS2 setup.




SD Card -> `writable/etc/netplan/50-wifis.yaml` edit the file as shown in the following images.

Default:

<img width="774" height="577" alt="image" src="https://github.com/user-attachments/assets/c0ab435a-b168-485c-a46d-d267ecaff308" />


Change it to:

<img width="1212" height="606" alt="image" src="https://github.com/user-attachments/assets/1f7a1881-3dc2-4b7c-babc-807c6ff5774d" />




On Windows Command Prompt:
`netsh wlan show interfaces` for wifi details.


Windows Command Prompt:
`ssh ubuntu@10.42.0.1`

<img width="453" height="496" alt="image" src="https://github.com/user-attachments/assets/4d3f6830-405e-4f51-9b0a-a83784198ef3" />


<img width="1482" height="762" alt="image" src="https://github.com/user-attachments/assets/b05a9b87-7f88-4f0f-b373-d7a004fea6d4" />


`sudo apt remove unattended-upgrades` ->  Before connecting to the internet, it’s important to disable automatic updates as you should initiate it according to your own availability. 
Otherwise, it will start updating the first time you get an internet connection. To prevent that, uninstall the “unattended-upgrades” package.

<img width="1483" height="762" alt="image" src="https://github.com/user-attachments/assets/89730738-2435-4868-93d7-81c22ac78fad" />


---

CREATE3 SETUP

Download file: 

Update:
<img width="1917" height="970" alt="image" src="https://github.com/user-attachments/assets/ec3fca0f-060b-4cfe-864e-99a52b7f3af7" />











Done setup wifi for raspi

<img width="1483" height="875" alt="image" src="https://github.com/user-attachments/assets/2a878b77-82f9-446d-bc83-9979d60c1eeb" />





---
NETWORKING - VERY IMPORTANT

<img width="1483" height="875" alt="image" src="https://github.com/user-attachments/assets/77160aad-0300-45d3-8fbb-5e9984eed9be" />


<img width="1919" height="967" alt="image" src="https://github.com/user-attachments/assets/5a312056-d80d-4710-8820-da66a2e9c39e" />


