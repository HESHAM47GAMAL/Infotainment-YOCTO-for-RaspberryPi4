# Infotainment-YOCTO-for-RaspberryPi4
## 📑 Table of Contents
- ⚡[Project_Overview](#Project_Overview)
- [🧠YOCTO_System_Requirements](#YOCTO_System_Requirements)
- [💾Flashing_to_SD_Card](#Flashing_to_SD_Card)

## ⚡ Project_Overview
This system image was **built using the Yocto Project (Kirkstone branch)** for the **Raspberry Pi 4 (64-bit)** target.  
It includes the following key packages and configurations:
- `SSH` — for remote access
- `WiFi` — for supporting wireless connectivity
- `Nano editor` — for basic text editing
- `Qt5` — for GUI application development
- `RPI` — for screen mirroring for MAC OS, and `scrcpy` — for screen mirroring for Android OS
- `ALSA` — for audio playing
- `Hello Application` — native C++ application

<img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/1.Infotainment_System.png">

<p>The project is built using **kernel version 5.15.x** and uses systemd as the init system</p>
<p>I will follow this **architecture overview**</p>

<img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/2.architecture%20overview.png">

## 🧠YOCTO_System_Requirements
- `Free Disk` — you need a system with at least 90 Gbytes of free disk space
- `RAM` — to build an image, you need at least 8 Gbytes of RAM
- `YOCTO` - current release **Kirkstone** supported in **Ubuntu 20.04** and **Ubuntu 22.04**

## 💾Flashing_to_SD_Card
