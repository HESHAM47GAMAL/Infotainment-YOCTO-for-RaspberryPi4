# Infotainment-YOCTO-for-RaspberryPi4
## 📑 Table of Contents
- ⚡[Project_Overview](#Project_Overview)
- [🧠YOCTO_System_Requirements](#YOCTO_System_Requirements)
- [YOCTO_Architecture](#YOCTO_Architecture)
- [Developmnent_Phases](#Developmnent_Phases)
  - [Pre-Development_Stage](#Pre-Development_Stage)
  - [Development_Stage](#Development_Stage)
  - [Post-Development_Stage](#Post-Development_Stage)


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
  
<img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/1.Infotainment_System.png">

this project is built using **kernel version 5.15.x** and uses **systemd** as the **init system**<br>
I will follow this **architecture overview**

<img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/2.architecture%20overview.png">

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

     <img src= "https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/22.PackageRecipeBuildFlowBuildSystem.png">
     
## Developmnent_Phases
<p>Development is divided into phases to facilitate Development process</p>

### Pre-Development_Stage
Here, prepare the host machine to be  ready to create an image using YOCTO

- Download Yocto extension in VS Code
  <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/3.YOCTO_extension.png">

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
  <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/4.poky_structure.png">

### Development_Stage
  Here, I will integrate and create all recipes needed to create an image<br>
  
  - Integrate **BSP layer** for Raspberry Pi 4 [Go to](https://layers.openembedded.org/layerindex/branch/master/layers/)

    search for **meta-raspberrypi** and choose it
      
    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/5.raspberryPi_layer.png">

    Here, look at the repo link

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/6.RaspberryRepoLink.png">
    
    This is confirmation that this BSP will provide full functionality for my HW

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/7.Machines.png">
    
    ```bash
    cd ~/YOCTO/poky
    git clone -b kirkstone git://git.yoctoproject.org/meta-raspberrypi
    source oe-init-build-env
    bitbake-layers add-layer ../meta-raspberrypi/
    ```
    this is test image provided you can use it to test that HW work right
    
    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/8.testImgae.png">
    
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

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/9.Meta_qt5.png">

    Here, look at the repo link and Dependencies

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/10.QTRepoLink.png">

    ```bash
    cd ~/YOCTO/poky
    git clone -b kirkstone https://github.com/meta-qt5/meta-qt5.git
    cd ~/YOCTO/poky/build
    bitbake-layers add-layer ../meta-qt5/
    ```
    during run command **bitbake-layers add-layer ../meta-qt5/** will face this error

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/11.ErrorIntegrateqt5.png">

    explain how this error introduced

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/12.qtResolveDependencies.png">

    So, will clone **openEmbedded-core**

    ```bash
    cd ~/YOCTO/poky
    git clone -b kirkstone git://git.openembedded.org/meta-openembedded
    cd ~/YOCTO/poky/meta-openembedded
    ls
    ```
    will find that it consists of multiple layers

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/13.recipesOpenEmbedded.png">

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

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/14.Meta-Poky_folderStructure.png">

    So fucos in this image to this folder distro and need to have same to new two distro layer
    
    ```bash
    meta-poky/
    ├──conf
    | ├── distro/
    | |└── include/
    | |└──layer.conf
    ```

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/15.IsLayerDistro.png">

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

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/16.infoDistroFeature.png">

    Here, define the Linux version

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/17.LinuxVersion">

    Here, include part responsible for define init process system

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/18.DefiningSystemD.png">
    
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

    <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/19.AudioFeatures.png">

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

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/20.NativePackageTree.png">     
      
       So, why was this recipe created with this structure **meta-ivi/recipes-native-cpp/helloworldd** 
        
       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/21.BitbakeSearchForRecipe.png">

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

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/23.FinalImageRecipeCall.png"> 

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
       
       will move to [Link](https://www.nano-editor.org/git.php)

       Press in link to **Instruction link**
       
       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/24.Nano_Site.png">

       will find that Nano has **prerequisites**

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/25.NanoPrerequisities.png">

       And there is dependency I need to resolve to compile without problems

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/26.NanoCompileDependency.png">

       To create **package recipe** will use **recipetool**

       ```bash
       cd ~/YOCTO/poky/meta-ivi
       mkdir recipes-editor
       cd recipes-editor
       mkdir nano
       cd nano
       recipetool create -o nano_1.0.bb https://git.savannah.gnu.org/git/nano.git
       ```
       this final content to **nano_1.0.bb**

       ```bash
       # Recipe created by recipetool
       # This is the basis of a recipe and may need further editing in order to be fully functional.
       # (Feel free to remove these comments when editing.)
      
       # WARNING: the following LICENSE and LIC_FILES_CHKSUM values are best guesses - it is
       # your responsibility to verify that the values are complete and correct.
       #
       # The following license files were not able to be identified and are
       # represented as "Unknown" below, you will need to check them yourself:
       #   COPYING.DOC
       #
       # NOTE: multiple licenses have been detected; they have been separated with &
       # in the LICENSE value for now since it is a reasonable assumption that all
       # of the licenses apply. If instead there is a choice between the multiple
       # licenses then you should change the value to separate the licenses with |
       # instead of &. If there is any doubt, check the accompanying documentation
       # to determine which situation is applicable.
       LICENSE = "GPL-3.0-only & Unknown"
       LIC_FILES_CHKSUM = "file://COPYING;md5=f27defe1e96c2e1ecd4e0c9be8967949 \
                          file://COPYING.DOC;md5=ad1419ecc56e060eccf8184a87c4285f"
      
       SRC_URI = "git://git.savannah.gnu.org/git/nano.git/;protocol=https;branch=master"
      
       # Modify these as desired
       PV = "1.0+git${SRCPV}"
       SRCREV = "50e186bc8162009c9efeb562d29f9aae70406eb4"
       
       S = "${WORKDIR}/git"
      
       # NOTE: the following library dependencies are unknown, ignoring: curses
       #       (this is based on recipes that have previously been built and packaged)
       DEPENDS = "file zlib ncurses"
      
       # NOTE: if this software is not capable of being built in a separate build directory
       # from the source, you should replace autotools with autotools-brokensep in the
       # inherit line
       inherit pkgconfig gettext autotools
      
       # Specify any options you want to pass to the configure script using EXTRA_OECONF:
       EXTRA_OECONF = ""
      
       AUTOTOOLS_AUTORECONF = "yes"
      
       do_configure(){
           oe_runconf
       }
       ```
       
       Look how **recipetool** is able to resolve dependency

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/27.RecipetoolResolve.png">

       To confirm that **package recipe** can build without any problem

       ```bash
       bitbake nano
       ```

       I hope that it is done without any problem, but you will almost face those problems
       First problem is **falling fetch process**

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/28.fetchingNanoSource.png">

       I overcome this problem by make cloning manually

       ```bash
       cd ~/YOCTO/poky/build/downloads/git2
       mkdir git.savannah.gnu.org.git.nano.git.
       git clone https://git.savannah.gnu.org/git/nano.git nano
       ```
       After this step, when making bitbake for **package recipe** again, you may face problem

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/29.ConfigTaskNano.png">

       I will move to the path that is expected to find **configuration folder**

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/30.checkConfigFolder.png">

       To generate a configuration script (in the same path you get, like the previous image, run this command)

       ```bash
       ./autogen.sh
       ```

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/31.ConfigFolderCreated.png">

       After those steps, when run this command expected to work smoothly without problems

       ```bash
       bitbkae nano
       ```

    5. Integrate **rpi** to be able to make screen mirroring from MAC devices
    
       To create **package recipe** will use **recipetool**

       ```bash
       cd ~/YOCTO/poky/meta-ivi
       mkdir recipes-info
       cd recipes-info
       mkdir rpiplay
       cd rpiplay
       recipetool create -o rpi-play.bb  https://github.com/FD-/RPiPlay.git
       ```

       content of **rpi-play.bb**

       ```bash
       # Recipe created by recipetool
       # This is the basis of a recipe and may need further editing in order to be fully functional.
       # (Feel free to remove these comments when editing.)
      
       # WARNING: the following LICENSE and LIC_FILES_CHKSUM values are best guesses - it is
       # your responsibility to verify that the values are complete and correct.
       #
       # The following license files were not able to be identified and are
       # represented as "Unknown" below, you will need to check them yourself:
       #   LICENSE
       #   lib/llhttp/LICENSE-MIT
       #   lib/playfair/LICENSE.md
       LICENSE = "Unknown"
       LIC_FILES_CHKSUM = "file://LICENSE;md5=1ebbd3e34237af26da5dc08a4e440464 \
                           file://lib/llhttp/LICENSE-MIT;md5=f5e274d60596dd59be0a1d1b19af7978 \
                           file://lib/playfair/LICENSE.md;md5=c7cd308b6eee08392fda2faed557d79a"
      
       SRC_URI = "git://github.com/FD-/RPiPlay.git;protocol=https;branch=master"
       
       # Modify these as desired
       PV = "1.0+git${SRCPV}"
       SRCREV = "64d0341ed3bef098c940c9ed0675948870a271f9"
       
       S = "${WORKDIR}/git"
      
       # NOTE: the following library dependencies are unknown, ignoring: openmaxil plist-2 vchiq_arm brcmGLESv2 vcos bcm_host plist brcmEGL
       #       (this is based on recipes that have previously been built and packaged)
       DEPENDS = "openssl"
      
       inherit cmake pkgconfig
      
       # Specify any options you want to pass to cmake using EXTRA_OECMAKE:
       EXTRA_OECMAKE = ""
       ```
       will find that the recipetool makes partially resolving to dependencies **DEPENDS = "openssl"** but when going to repo link, will find a lot of dependencies need to resolved

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/32.rpi_depencies.png">

       First, I will confirm that these dependencies exist in any layer I have
       Check if I have **avahi** and can also confirm if you have it by run

       ```bash
       cd ~/YOCTO/poky/
       find . -name "avahi"
       ```

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/33.avahiRecipe.png">

       Check if I have **plist**

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/34.plistDependencies.png">

       After confirming that I have all of those packages in any layer, I can add them to **dependencies** without problems

       So update this variable in **rpi-play.bb** to be

       ```bash
       DEPENDS = "avahi libplist openssl"
       ```

    6. Integrate **Audio** to be able to play sound

       will use ALSA tool that has this Architecture

       <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/35.SoundLayers.png">

       will create **class recipe** to install recipes needed to support audio

       ```bash
       cd ~/YOCTO/poky/meta-ivi
       mkdir classes
       cd classes/
       touch audio.bbclass
       ```
       Content of **audio.bbclass**

       ```bash
       IMAGE_INSTALL:append = " packagegroup-rpi-test pavucontrol pulseaudio  pulseaudio-module-dbus-protocol pulseaudio-server pulseaudio-module-loopback pulseaudio-module-bluetooth-discover alsa-ucm-conf pulseaudio-module-bluetooth-policy alsa-topology-conf alsa-state alsa-lib alsa-tools pulseaudio-module-bluez5-discover libsocketcan\
       pulseaudio-module-bluez5-device alsa-utils alsa-plugins rpi-play can-utils net-tools gstreamer1.0 iproute2 iputils qtbase-examples qtquickcontrols qtbase-plugins qtquickcontrols2 qtgraphicaleffects qtmultimedia qtserialbus qtquicktimeline qtvirtualkeyboard bluez5 bluez5-noinst-tools i2c-tools hostapd iptables packagegroup-tools-bluetooth gstreamer1.0-plugins-base gstreamer1.0-plugins-good "
       ```
       To include those packages in final image, I need to update **ivi-test-image.bb** that is **image recipe**

       ```bash
       # 1. include base image
       require recipes-sato/images/core-image-sato.bb
       
       # 2. set of local varaibles

       SUMMARY = "A simple IVI test image that include rpi function + helloworld package recipe"

       inherit audio

       # 3. IMAGE_INSTALL 
       IMAGE_INSTALL:append = " worldhello openssh rpi-play nano dhcpcd net-tools"

       # 4. IMAGE_FEATURES
       # 1. install ssh
       # 2. allow root access through ssh
       # 3. access root through ssh using empty password
       IMAGE_FEATURES:append = " debug-tweaks ssh-server-openssh"
       ```
       I will **systemd** as init process, so I will update **DISTRO** variable in **local.conf**

       ```bash
       cd ~/YOCTO/poky/build/conf
       vi local.conf
       #change DISTRO variable to be infotainment
       DISTRO ?= "infotainment"
       ```
	7. Integrate **scrcpy** to enable screen mirroring for Android

	   can reach repo [Link](https://github.com/Genymobile/scrcpy/tree/master)
	   Let's prepare **package recipe**

	   ```bash
       cd ~/YOCTO/poky/meta-ivi/recipes-info
       mkdir scrcpy
       cd scrcpy
       recipetool create -o scrcpy_1.0.bb https://github.com/Genymobile/scrcpy
       ```
       In this repo, there are build instructions [Link](https://github.com/Genymobile/scrcpy/blob/master/doc/build.md)
       
	   From this repo understand that **Scrcpy** uses **a client–server architecture**.
     
       Let's break down this **Architecture**<br>
     	- **Server (on Android phone)**<br>
      		A small Java .jar file called scrcpy-server runs inside your Android device.<br>
            It captures the screen and sends the encoded video stream (via ADB) back to the host (Raspberry Pi in your case).<br>
       	    It also receives control inputs (keyboard, mouse) from the host.<br>
	    - **Client (on Raspberry Pi)**<br>
      		The compiled binary /usr/bin/scrcpy is the client application.<br>
			It starts an ADB connection, pushes the server JAR to the phone, starts it there, and then opens a video window (via SDL2).<br>
			It decodes the video stream using FFmpeg and shows the screen interactively.<br>
			
	   Based on this Information, I need to get **scrcpy-server**. It exists in **build instruction Guidance**. as It need to be exist to complete build without any problem<br>

	   <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/38.Scrcpy-server.png">

	   ```bash
       cd ~/YOCTO/poky/meta-ivi/recipes-info/scrcpy
       mkdir files
       cd files
       wget https://github.com/Genymobile/scrcpy/releases/download/v3.3.3/scrcpy-server-v3.3.3 -O scrcpy-server.jar
       #remove extension .jar
       ```

	   Content of **scrcpy_1.0.bb**

       ```bash
       SUMMARY = "Display and control Android devices"
	   DESCRIPTION = "Scrcpy provides display and control of Android devices connected via USB or TCP/IP."
	   HOMEPAGE = "https://github.com/Genymobile/scrcpy"
	   LICENSE = "Apache-2.0"
	   LIC_FILES_CHKSUM = "file://LICENSE;md5=798340522dc9eb3d3568e42ca2612bb0"
	
	   # Sources:
	   # - main source from GitHub
	   # - server JAR provided manually in 'files/' folder
	   SRC_URI = "git://github.com/Genymobile/scrcpy.git;protocol=https;branch=master \
		   file://scrcpy-server \
		   "
	
	   PV = "3.3.3+git${SRCPV}"
	   SRCREV = "e5e58b1b307d92cbe3432431a9e22cd648f8d4d1"
	
	   S = "${WORKDIR}/git"
	
	
	   DEPENDS = "ffmpeg libsdl2 libusb libdrm meson-native ninja-native"
	   RDEPENDS:${PN} += "android-tools"
	
	   inherit meson pkgconfig
	
	   # Disable building the server (we provide our own)
	   # EXTRA_OEMESON += "-Dbuild_server=false"
	
	
	   # --- FIX: patch meson.build to skip server ---
	   do_configure:prepend() {
		   # comment out any "subdir('server')" line to avoid missing build file errors
		   sed -i '/subdir.*server/d' ${S}/meson.build
	   }
	
	   do_install() {
		   install -d ${D}${bindir}
		   install -m 0755 ${B}/app/scrcpy ${D}${bindir}/scrcpy
	
		   install -d ${D}${datadir}/scrcpy
		   install -m 0644 ${WORKDIR}/scrcpy-server ${D}${datadir}/scrcpy/scrcpy-server
	   }
	
	   FILES:${PN} += "${datadir}/scrcpy"
	   ```

	   Explain some points in **pcakge Recipe**

	   <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/39.explainScrcpyRecipe.png">	

    🙌🙌Finally, to create a **Linux Image Distribution**

    update **ivi-test-image.bb** 

    ```bash
    # 1. include base image --->
    # 1. Poky
    # 2. BSP 
    require recipes-sato/images/core-image-sato.bb

    # 2. set of local varaibles

    SUMMARY = "A simple IVI test image that include rpi function + helloworld package recipe"

    inherit audio

    # 3. IMAGE_INSTALL 
    IMAGE_INSTALL:append = " worldhello openssh rpi-play nano scrcpy android-tools dhcpcd net-tools"

    # 4. IMAGE_FEATURES
    # 1. install ssh
    # 2. allow root access through ssh
    # 3. access root through ssh using empty password
    IMAGE_FEATURES:append = " debug-tweaks ssh-server-openssh"
    ```

    ```bash
    bitbake ivi-test-image
    ```
	   
### Post-Development_Stage

  1. To be able to create Raspberry Pi image and flash it on SD card need to have this script

     ```bash
     cd ~/YOCTO/
     mkdir custom_scripts
     cd custom_scripts
     touch flashing.sh
     ```

     content of **flashing.sh**

     ```bash
     #!/usr/bin/bash

     function sdcard-flashing(){
    
     if [[ "$1" == "--help" ]]; then
         printf "%s\n%s\n" "1st parameter: /dev/<driver>" "2nd parameter: <image iso>"
         return 0
     fi
    

     if (( $# < 2 )); then
         echo "Invalid arguments passed use --help for valid options."
         return 1
     fi

     declare DRIVER=$1
     declare IMAGE_PATH=$2
     
     sudo dd if="${IMAGE_PATH}" of="${DRIVER}" status=progress

     return 0
     }


     function create-rpi-image(){

     if [[ "$1" == "--help" ]]; then
         printf "%s\n%s" "1st parameter: image-name" "RUN: wic list images"
         return 0
     fi
    

     if (( $# < 1 )); then
         echo "Invalid arguments passed use --help for valid options."
         return 1
     fi

     declare IMAGE_NAME=$1
    
     wic create  sdimage-raspberrypi -e ${IMAGE_NAME}

     return 0
     }
     ```

     need to source this script to be loaded when any terminal open

     ```bash
     nano ~/.bashrc
     # at the end of file, add those lines
     if [ -f "${HOME}/Hazem_Course/YOCTO/custom_scripts/flashing.sh" ]; then 
	   source "${HOME}/Hazem_Course/YOCTO/custom_scripts/flashing.sh"
     fi
     ```
  2. Let's create image

     to do this will create folder as any time will create image should exist in this path and run command

     ```bash
     cd cd ~/YOCTO/poky
     mkdir rasp-images
     cd rasp-images
     create-rpi-image ivi-test-image
     ```
     will get log like this and inform you image created

     <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/36.imageCreated.png">
    
  3. 📥Load SD card with image

     To avoid any problem, please confirm that SD card formatted (writing zero values)
     
     Plug SD card into laptop

     run this command to know user SD card name to write image for it (in my case SD card read as **sdb**)

     ```bash
     lsblk
     ```
     Then write image to your sd card

     <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/37.flashSD.png">

	 then unplug SD card and insert it into Raspberry pi

  4. make SSH for your target machine

     Confirm that your raspberry pi Ethernet pluged (your laptop and raspberry pi should conencted with wired ethernet in same network)<br>
	 I need to see network configuration

     ```bash
     ifconfig
     ```

	 will see something like this

	 <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/40.ifconfig.png">	
		
	 As, I infrom we use wired so will focus with **enp7s0** as I have IP = 192.168.1.8

 	 so will try to see IP that has same subnet **192.168.1.0/24**

	 ```bash
     nmap -sn 192.168.1.0/24
     ```

     and will get something like this 

	 <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/41.HostIP.png">

	 will try to **SSH** to your target machine (will try IP you get)

 	 ```bash
     ssh root@192.168.1.3
     ```
     until get something like

	 <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/42.SSH.png">

	 **🚩🚩👀👀Note at this point Screen may not work right**

  6. 🖥️Make Screen run without any problem

	 to make screen work without problems as I use **7 inch LCD Display-C**

	 will SSH your board again then do

	 ```bash
     cd nano /boot/config.txt
     ## add those lines
  	 # Enable VC4 Graphics (FKMS, stable with non-standard screens)
	 dtoverlay=vc4-fkms-v3d

	 # USB / HDMI tweaks
	 max_usb_current=1
	 config_hdmi_boost=7
	 hdmi_force_hotplug=1
	 
	 # Custom resolution for 7" LCD
	 hdmi_group=2
	 hdmi_mode=87
	 hdmi_cvt=1024 600 60 6 0 0 0
	 hdmi_drive=1
	
	 # Display tweaks
	 display_rotate=0
	 disable_overscan=0
	 max_framebuffers=2
 	 ```
     	 
     press **Ctrl + o** then **Enter** then **Ctrl + x**

	 then make board reboot

	 ```bash
     reboot
     ```

	 after reboot will get screen view

     <img src="https://github.com/HESHAM47GAMAL/Infotainment-YOCTO-for-RaspberryPi4/blob/main/images/43.Screnview.jpeg">

