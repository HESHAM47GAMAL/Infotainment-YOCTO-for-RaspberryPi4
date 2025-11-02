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

this project is built using **kernel version 5.15.x** and uses **systemd** as the **init system**<br>
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
 - **Package recipe** life cycle

     <img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/22.PackageRecipeBuildFlowBuildSystem.png">
     
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
  
  You can see more about releases [link](https://wiki.yoctoproject.org/wiki/Releases).

- Clone Poky Kirkstone version  
  To clone Poky, run this command in the path you want (will assume that current path is **~/YOCTO** and will run following command)
  
  ```bash
  git clone -b kirkstone https://github.com/yoctoproject/poky
  ```
  So, poky path is **~/YOCTO/poky**
  
- Understand **Poky**

  **Poky** is the **reference** distribution of the Yocto Project — it’s not a Linux distribution itself, but rather a build system (based on BitBake) and a set of metadata that allow you to build your own custom Linux distribution.
  - `Bitbake` — The build engine — parses recipes, resolves dependencies, and executes build tasks.
  - `Metadata` - Describe what to build and how to build it. This includes recipes (.bb), classes (.bbclass), and configuration files (.conf).
  - `OpenEmbedded` - The core layer that provides the essential Linux components and build metadata
  - `Metadata-poky` - Poky-specific configuration layer
  <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/4.poky_structure.png">

### Development_Stage
  Here, I will integrate and create all recipes needed to create an image<br>
  
  - Integrate **BSP layer** for Raspberry Pi 4 [Go to](https://layers.openembedded.org/layerindex/branch/master/layers/)

    search for **meta-raspberrypi** and choose it
      
    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/5.raspberryPi_layer.png">

    Here, look at the repo link

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/6.RaspberryRepoLink.png">
    
    This is confirmation that this BSP will provide full functionality for my HW

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/7.Machines.png">
    
    ```bash
    cd ~/YOCTO/poky
    git clone -b kirkstone git://git.yoctoproject.org/meta-raspberrypi
    source oe-init-build-env
    bitbake-layers add-layer ../meta-raspberrypi/
    ```
    this is test image provided you can use it to test that HW work right
    
    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/8.testImgae.png">
    
    you need to change machine that will create image for 
    
    ```bash
    cd cd ~/YOCTO/poky/build/conf
    vi local.conf
    # change MACHINE variable from qemuarm64 to raspberrypi4-64 to be final
    MACHINE ??= "raspberrypi4-64"
    # in same file add following lines that control number of core used during running build engine(bitbake) this mean that I will use 8 cores from CPu as I have 12 core you can know number of cores you have $lscpu
    BB_NUMBER_THREADS="8"
    PARALLEL_MAKE="-j 8"
    ```
    
    will run this command to confirm the generation image is complete without problem

    ```bash
    bitbake rpi-test-image
    ```
  - Integrate **SW layer** (Qt-5) [Go to](https://layers.openembedded.org/layerindex/branch/master/layers/)
    
    search for **meta-qt5** and choose it

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/9.Meta_qt5.png">

    Here, look at the repo link and Dependencies

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/10.QTRepoLink.png">

    ```bash
    cd ~/YOCTO/poky
    git clone -b kirkstone https://github.com/meta-qt5/meta-qt5.git
    cd ~/YOCTO/poky/build
    bitbake-layers add-layer ../meta-qt5/
    ```
    during run command **bitbake-layers add-layer ../meta-qt5/** will face this error

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/11.ErrorIntegrateqt5.png">

    explain how this error introduced

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/12.qtResolveDependencies.png">

    So, will clone **openEmbedded-core**

    ```bash
    cd ~/YOCTO/poky
    git clone -b kirkstone git://git.openembedded.org/meta-openembedded
    cd ~/YOCTO/poky/meta-openembedded
    ls
    ```
    will find that it consists of multiple layers

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/13.recipesOpenEmbedded.png">

    From  Dependencies for **Qt5** we only interested **meta-oe** layer

    ```bash
    cd ~/YOCTO/poky/build
    bitbake-layers add-layer meta-openembedded/meta-oe
    bitbake-layers add-layer ../meta-qt5/
    ```
  - Create **SW Layer**
    
    This layer will be used to integrate all layers. Follow the next command lines to create a  layer and add it to **bblayers.conf**

    ```bash
    cd ~/YOCTO/poky
    bitbake-layers create-layer meta-ivi
    cd ~/YOCTO/poky/build
    bitbake-layers add-layer ../meta-ivi/
    ```
    
  - Create **Distro Layer**
    
    to define the type of init system. What are init systems?
    When Linux boots, the **init system** is the **first process** started by the kernel **(PID 1)**.
    Its job is to:
    1. Initialize the system
    2. Start essential background services (daemons)
    3. Manage processes
    4. Handle shutdown and reboot
    So I will create two Distro layers  **meta-info-distro** that will use **systemd** and **meta-audio-distro** that will use **systemv**

    ```bash
    cd ~/YOCTO/poky
    bitbake-layers create-layer meta-info-distro
    bitbake-layers create-layer meta-audio-distro
    cd ~/YOCTO/poky/build
    bitbake-layers add-layer ../meta-info-distro/
    bitbake-layers add-layer ../meta-audio-distro/
    ```
    As we know, **Layer** is a directory, and to make it a **Distro layer**, you need to have a specific folder structure
    so will take Poky layer structure as an example

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/14.Meta-Poky_folderStructure.png">

    So fucos in this image to this folder distro and need to have same to new two distro layer
    
    ```bash
    meta-poky/
    ├──conf
    | ├── distro/
    | |└── include/
    | |└──layer.conf
    ```

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/15.IsLayerDistro.png">

    Let's prepare **info distro**

    ```bash
    cd ~/YOCTO/poky/meta-info-distro/conf
    mkdir distro
    cd distro/
    mkdir include
    touch infotainment.conf
    cd include/
    touch systemd.inc
    ```
    content of **infotainment.conf**
    
    ```bash
    DISTRO="infotainment"
    DISTRO_NAME="Bullet-Infotainment"
    DISTRO_VERSION="1.0"
    
    MAINTAINER="heshamgamal.a.h@gmail.com"
    
    
    # SDK Information.
    SDK_VENDOR = "-bulletSDK"
    SDK_VERSION = "${@d.getVar('DISTRO_VERSION').replace('snapshot-${METADATA_REVISION}', 'snapshot')}"
    SDK_VERSION[vardepvalue] = "${SDK_VERSION}"
    
    SDK_NAME = "${DISTRO}-${TCLIBC}-${SDKMACHINE}-${IMAGE_BASENAME}-${TUNE_PKGARCH}-${MACHINE}"
    # Installation path --> can be changed to ${HOME}-${DISTRO}-${SDK_VERSION}
    SDKPATHINSTALL = "/opt/${DISTRO}/${SDK_VERSION}" 
    
    # Disribution Feature --> NOTE: used to add customize package (for package usage).
    
    # infotainment --> INFOTAINMENT
    
    INFOTAINMENT_DEFAULT_DISTRO_FEATURES = "largefile opengl ptest multiarch vulkan x11 bluez5 bluetooth wifi qt5"
    INFOTAINMENT_DEFAULT_EXTRA_RDEPENDS = "packagegroup-core-boot"
    INFOTAINMENT_DEFAULT_EXTRA_RRECOMMENDS = "kernel-module-af-packet"
    
    # TODO: to be org.
    
    DISTRO_FEATURES ?= "${DISTRO_FEATURES_DEFAULT} ${INFOTAINMENT_DEFAULT_DISTRO_FEATURES} userland"
    
    #add systemd as init-process
    require conf/distro/include/systemd.inc
    
    # prefered version for packages.
    PREFERRED_VERSION_linux-yocto ?= "5.15%"
    PREFERRED_VERSION_linux-yocto-rt ?= "5.15%"
    
    
    # Build System configuration.
    
    LOCALCONF_VERSION="2"
    
    # add poky sanity bbclass
    INHERIT += "poky-sanity"
    ```
    content of **systemd.inc**

    ```bash
    #install systemd as default init system manager
    DISTRO_FEATURES:append=" systemd" 
    # select systemd as init manager 
    VIRTUAL-RUNTIME_init_manager="systemd"
    VIRTUAL-RUNTIME_initscripts="systemd-compat-units"
    ```
    This Distro will have the following Features

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/16.infoDistroFeature.png">

    Here, define the Linux version

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/17.LinuxVersion">

    Here, include part responsible for define init process system

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/18.DefiningSystemD.png">
    
    Let's prepare **audio distro**
    
    ```bash
    cd ~/YOCTO/poky/meta-audio-distro/conf
    mkdir distro
    cd distro/
    touch audio.conf
    ```  
    content of **audio.conf**

    ```bash
    DISTRO="audio"
    DISTRO_NAME="Bullet-audio"
    DISTRO_VERSION="1.0"
    
    MAINTAINER="heshamgamal.a.h@gmail.com"
    
    
    # SDK Information.
    SDK_VENDOR = "-bulletSDK"
    SDK_VERSION = "${@d.getVar('DISTRO_VERSION').replace('snapshot-${METADATA_REVISION}', 'snapshot')}"
    SDK_VERSION[vardepvalue] = "${SDK_VERSION}"
    
    SDK_NAME = "${DISTRO}-${TCLIBC}-${SDKMACHINE}-${IMAGE_BASENAME}-${TUNE_PKGARCH}-${MACHINE}"
    # Installation path --> can be changed to ${HOME}-${DISTRO}-${SDK_VERSION}
    SDKPATHINSTALL = "/opt/${DISTRO}/${SDK_VERSION}" 
    
    # Disribution Feature --> NOTE: used to add customize package (for package usage).
    
    # infotainment --> INFOTAINMENT
    
    AUDIO_DEFAULT_DISTRO_FEATURES = "largefile opengl ptest multiarch vulkan bluez5 bluetooth wifi audio_only"
    AUDIO_DEFAULT_EXTRA_RDEPENDS = "packagegroup-core-boot"
    AUDIO_DEFAULT_EXTRA_RRECOMMENDS = "kernel-module-af-packet"
    
    # TODO: to be org.
    
    DISTRO_FEATURES ?= "${DISTRO_FEATURES_DEFAULT} ${AUDIO_DEFAULT_DISTRO_FEATURES} userland"
    
    
    # prefered version for packages.
    PREFERRED_VERSION_linux-yocto ?= "5.15%"
    PREFERRED_VERSION_linux-yocto-rt ?= "5.15%"
    
    
    # Build System configuration.
    
    LOCALCONF_VERSION="2"
    
    # add poky sanity bbclass
    INHERIT += "poky-sanity"
    ```
    
    This Distro will have the following Features

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/19.AudioFeatures.png">

    I will use the same Linux version, and by default **systemv** used
  - Inside **meta-ivi** layer will add different types of recipes to create my own **Image recipe**
    1. Add a Native Cpp application
       
       ```bash
       cd ~/YOCTO/poky/meta-ivi
       mkdir recipes-native-cpp
       cd recipes-native-cpp/
       mkdir helloworldd
       ```

       I will use **recipetool** to get code from upstream

       ```bash
       cd ~/YOCTO/poky/meta-ivi/recipes-native-cpp/helloworldd
       recipetool create -o worldhello_1.1.bb https://github.com/embeddedlinuxworkshop/y_t1.git
       ```
       What is the  final folder structure for the new package recipe?

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/20.NativePackageTree.png">     
      
       So, why was this recipe created with this structure **meta-ivi/recipes-native-cpp/helloworldd** 
        
       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/21.BitbakeSearchForRecipe.png">

       Final content to the recipe to build it without problems

       ```bash
       # Recipe created by recipetool
       # This is the basis of a recipe and may need further editing in order to be fully functional.
       # (Feel free to remove these comments when editing.)
       
       # TODO 1. Documentation varaibles 
       SUMMARY = "Hello World example in C++"
       DESCRIPTION = "A simple C++ Hello World example"
       #use it if the
       # HOMEPAGE = "https://github.com/embeddedlinuxworkshop/y_t1" 
        
        
       # Unable to find any files that looked like license statements. Check the accompanying
       # documentation and source headers and set LICENSE and LIC_FILES_CHKSUM accordingly.
       #
       # NOTE: LICENSE is being set to "CLOSED" to allow you to at least start building - if
       # this is not accurate with respect to the licensing of the software being built (it
       # will not be in most cases) you must specify the correct value before using this
       # recipe for anything other than initial testing/development!
       #
       # TODO 2 License Varaibles . 
       LICENSE = "CLOSED"
       LIC_FILES_CHKSUM = ""
        
        
       # TODO 3. Source code varaibles  
       SRC_URI = "git://github.com/embeddedlinuxworkshop/y_t1.git;protocol=https;branch=master"
        
       # Modify these as desired
       PV = "1.1+git${SRCPV}"
       SRCREV = "49600e3cd69332f0e7b8103918446302457cd950"
        
       S = "${WORKDIR}/git"
        
       # TODo 4. Tasks to be executed 
        
       # NOTE: no Makefile found, unable to determine what needs to be done
        
       do_configure () {
       	# Specify any needed configure commands here
       	:
       }
        
       do_compile () {
       	# Specify compilation commands here
       	$CXX ${S}/main.cpp -o worldhello
       }
        
        
        
       do_install () {
        #  manp ---> ${WOPRKDIR}/image
        # specify install commands here
        # 1. create directory ${WOPRKDIR}/image/usr/bin
        install -d ${D}/${bindir}
        # 2. install worldhello binary to ${WOPRKDIR}/image/usr/bin
        install -m 0755 worldhello ${D}/${bindir}
       }
       # ignore do_package
       do_package_qa[noexec] = "1"
       ```
       Run the following command to build

       ```bash
       bitbake worldhelllo
       ```
    2. Create my own **Recipe Image**
       An image recipe (*.bb) defines **the final root filesystem (image)** that Yocto will build — that is, the actual system that runs on your target(Raspberry Pi 4).Unlike a **package recipe** (which builds a single app or library), an **image recipe** tells **Yocto**:“What packages should be installed into the final image, and how should it be built and formatted (ext4, wic, sdcard, etc.).”
       
       To create your own **image recipe** there is Base should start from it. Will use **core-image-sato** as base

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/23.FinalImageRecipeCall.png"> 

       There is standard structure for **Image**. currently I will create this structure

       ```bash
       cd ~/YOCTO/poky/meta-ivi
       mkdir recipes-core
       cd recipes-core/
       mkdir images
       cd images/
       touch ivi-test-image.bb
       ```
       content of **ivi-test-image.bb**  will include (native cpp application)
       **IMAGE_INSTALL** is a variable used in **image recipes** (*.bb) to tell Yocto which packages should be included inside the **final root filesystem image** that will run on your device
       
       ```bash
       # 1. include base image 
       require recipes-sato/images/core-image-sato.bb
      
       # 2. set of local varaibles
      
       SUMMARY = "A simple IVI test image that include rpi function + helloworld package recipe"
      
       # 3. IMAGE_INSTALL 
       IMAGE_INSTALL:append = " worldhello"
       ```
       Confirm that everything work right

       ```bash
       bitbake ivi-test-image
       ```
    3. Integrate **SSH** and be able to access root through SSH using empty password
       There are two variables. I think there is need to provide clear define for them
       
       `IMAGE_FEATURES` - is a variable used to define a set of high-level features or attributes that an image should possess. These features, when specified, instruct the Yocto build system to automatically include the necessary packages, configurations, or modifications to achieve the desired functionality in the final image.
       `IMAGE_INSTALL` - is a variable used in **image recipes** (*.bb) to tell Yocto which packages should be included inside the **final root filesystem image** that will run on your device

       so to do this will update **ivi-test-image** content

       ```bash
       # 1. include base image 
       require recipes-sato/images/core-image-sato.bb
       
       # 2. set of local varaibles
      
       SUMMARY = "A simple IVI test image that include rpi function + helloworld package recipe"

       # 3. IMAGE_INSTALL 
       IMAGE_INSTALL:append = " worldhello openssh dhcpcd net-tools"
      
       # 4. IMAGE_FEATURES
       # 1. install ssh
       # 2. allow root access through ssh
       # 3. access root through ssh using empty password
       IMAGE_FEATURES:append = " debug-tweaks ssh-server-openssh"
       ```

    4. Integrate **Nano Editor** to be able to open any file and edit on the target machine     
        
       
  
### Post-Development_Stage

## 💾Flashing_to_SD_Card
