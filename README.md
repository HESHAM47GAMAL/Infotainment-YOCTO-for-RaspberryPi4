# Infotainment-YOCTO-for-RaspberryPi4
## 📑 Table of Contents
- ⚡[Project_Overview](#Project_Overview)
- [🧠YOCTO_System_Requirements](#YOCTO_System_Requirements)
- [YOCTO_Architecture](#YOCTO_Architecture)
- [Developmnent_Phases](#Developmnent_Phases)
  - [Pre-Development_Stage](#Pre-Development_Stage)
  - [Development_Stage](#Development_Stage)
  - [Post-Development_Stage](#Post-Development_Stage)
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

this project is built using **kernel version 5.15.x** and uses systemd as the init system<br>
I will follow this **architecture overview**

<img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/2.architecture%20overview.png">

## 🧠YOCTO_System_Requirements
- `Free Disk` — you need a system with at least 90 Gbytes of free disk space
- `RAM` — to build an image, you need at least 8 Gbytes of RAM
- `YOCTO` - current release **Kirkstone** supported in **Ubuntu 20.04** and **Ubuntu 22.04**

## YOCTO_Architecture
 - ### Layer
   - **Layer** in Yocto is a modular collection of metadata that defines part of your Linux build
   - In technically definition **layer** is a directory containing BitBake metadata such as
     1. Recipes (.bb)
     2. Classes (.bbclass)
     3. Configuration files (.conf)
   - Layers type
      - `BSP Layer` - provides all hardware-specific support needed to run Linux on a target board. It defines how the kernel, bootloader, and device drivers are built and configured for a specific SoC **(like meta-raspberrypi)**
      - `SW Layer` - contains higher-level software components that are independent of the hardware. It’s used to add or customize packages, applications, libraries, and services that run on top of the system **(like meta-qt5)**
      - `Distribution Layer` - defines policies and settings that determine how the overall system behaves and is built. It provides a higher-level configuration that specifies which layers to include, which init system to use, and how images should be composed **(like poky)**
 - ### Recipe
   - **Recipe** tells BitBake how to build a specific piece of software or system component
   - Recipes type
     - `Package Recipe`
       - **Purpose** : Build and install a single software package
       - **Description** : package recipe tells how to fetch, compile, and install a specific software component. It defines dependencies, source location, build instructions, and install rules.
     - `Image Recipe`
       - **Purpose** : Define the final root filesystem image
       - **Description** : An image recipe specifies which packages and configurations should be included in the final system image.
     - `Class Recipe`
       - **Purpose** : Define common reusable behavior shared between multiple recipes
       - **Description** : class recipe (.bbclass file) acts like a template or helper that contains common functions, tasks, and variable definitions.
     - `Configuration Recipe`
       - **Purpose** : Define **global build settings**, **machine configurations**, and **distribution** policies.
       - **Description** : Configuration files tell BitBake how to build the system rather than what to build. They live in .conf files (not .bb) and define settings for:
          - machine (hardware-specific)
          - distro (distribution-wide)
          - layer (metadata paths)
          - local build environment
       - Common files:
          - local.conf → your build-specific settings (set in build/conf/)
          - bblayers.conf → lists which layers are included
          - machine.conf → defines hardware (e.g., MACHINE = "raspberrypi4")
          - distro.conf → defines global policies (e.g., init system, package manager) 

## Developmnent_Phases
<p>Development is divided into phases to facilitate Development process</p>

### Pre-Development_Stage
Here, prepare the host machine to be  ready to create an image using YOCTO

- Download Yocto extension in VS Code
  <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/3.YOCTO_extension.png">

- Install Yocto dependencies
  ```bash
  sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev python3-subunit mesa-common-dev zstd liblz4-tool file locales libacl1
  sudo locale-gen en_US.UTF-8
  ```
- Choose YOCTO Release
  <p>Here, I will use Kirkstone version</p>
  <p>You can see more about releases [link](https://wiki.yoctoproject.org/wiki/Releases)</p>

- Clone Poky Kirkstone version  
  <p>To clone Poky, run this command in the path you want </p>
  
  ```bash
  git clone -b kirkstone https://github.com/yoctoproject/poky
  ```
  
- Understand Poky
  **Poky** is the **reference** distribution of the Yocto Project — it’s not a Linux distribution itself, but rather a build system (based on BitBake) and a set of metadata that allow you to build your own custom Linux distribution.
  - `Bitbake` — The build engine — parses recipes, resolves dependencies, and executes build tasks.
  - `Metadata` - Describe what to build and how to build it. This includes recipes (.bb), classes (.bbclass), and configuration files (.conf).
  - `OpenEmbedded` - The core layer that provides the essential Linux components and build metadata
  - `Metadata-poky` - Poky-specific configuration layer
  <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/4.poky_structure.png">

### Development_Stage
  Here, I will integrate and create all recipes needed to create an image
  
### Post-Development_Stage

## 💾Flashing_to_SD_Card
