# BUILD IMAGE "BOOT2QT" FOR RASPBERRY PI 4 ON YOCTO PROJECT
---
### What is Yocto Project?

- Yocto is an open-source project that provides a **build framework and metadata** to create custom Linux images for embedded boards.
- It uses **layers** (collections of recipes, classes, and configs) and **recipes (.bb files)** that define build instructions.
- The **main build tool is BitBake**, which parses recipes, gathers source code, compiles software, and packages everything into an image.
- Its reference Linux distribution is called **Poky**.
- Yocto is mainly used for **embedded Linux devices** to build tailored distributions for specific hardware.

### What is boot2qt?

- boot2qt is a set of Yocto layers that builds a **minimal Linux system** that boots **directly into a Qt application** (no desktop environment).
- It provides a **cross-compile SDK** for your desktop, used with Qt Creator to build apps for your target device.
- It **integrates with Qt Creator** for easy deploy and run on your embedded device via the network.
- In short, **boot2qt builds on top of Yocto** to provide Qt integration, saving development time for embedded Qt projects.

## 1. Install the required packages first:
>Notes: My build machine runs Ubuntu 20.04 for the Qt 5.15 build and i recommend run on machine Ubuntu 20.04 or 18.04 

```bash
sudo apt-get install gawk wget git diffstat unzip texinfo gcc build-essential \
chrpath socat cpio python3 python3-pip python3-pexpect xz-utils \
debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa \
libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev python \
git-lfs g++-multilib gcc-multilib libxkbcommon-dev \
libxkbcommon-x11-dev libwayland-cursor++0 libwayland-cursor0
```

Next you need repo, a git management tool **by Google**. It hides most of the complexity of submodules and different repo's by putting all of that extra information in a manifest file. You can install it using apt:

```bash
sudo apt install repo
```

## 2. Create a manifest (file.xml), initialize and sync the repo.

**STEP1: Create a new folder where all of yocto will reside.** 

```bash
mkdir b2qt
cd b2qt
```

> **Recommendation:**  
> I recommend that you create your own manifest file for your Git repo to suit your project, instead of using the manufacturer's manifest that includes everything.

**STEP2: Create a new file manifest.xml.** 

You can refer to my `manifest.xml` file through the following link: [https://github.com/BocD-210/b2qt_mainfest](https://github.com/BocD-210/b2qt_mainfest) this manifest.xml commit the QT version 5 use for Raspberry.

**STEP3: Initialize and sync the repo.**

Use the `repo` tool to clone all the git repositories of the Yocto project and boot2qt at once. First initialize for the correct Qt version:

```bash
repo init -u https://github.com/BocD-210/b2qt_mainfest.git -m manifest.xml
```

Alternatively, you can initialize the repo using the manufacturer’s manifest for Qt 5.15 or Qt 6.2 with the following commands:

```bash
# For Qt 6.2:
repo init -u git://code.qt.io/yocto/boot2qt-manifest -m v6.2.3.xml

# For Qt 5.15:
repo init -u git://code.qt.io/yocto/boot2qt-manifest -m v5.15.2.xml
```

Then sync the repositories:

```bash
repo sync
```

## 3. Building the image boot2qt for Raspberry pi 4.

You must tell which board you want to build for, then source a script:

```bash
export MACHINE=raspberrypi4 && source ./setup-environment.sh 
```
>**Note:** Here, the image is built for the Raspberry Pi 4.  
>You should change the `MACHINE` configuration to match your target device.
>**AND: This step must be repeated on every login.**

Output:

```
### Shell environment set up for builds. ###

You can now run 'bitbake <target>'

Common targets are:
b2qt-embedded-qt6-image
meta-toolchain-b2qt-embedded-qt6-sdk

QBSP target is:
meta-b2qt-embedded-qbsp

For creating toolchain or QBSP for Windows, set environment variable before running bitbake:
SDKMACHINE=x86_64-mingw32

For more information about Boot to Qt, see https://doc.qt.io/QtForDeviceCreation/
```

**Then you building image boot2qt:**

>The following command builds the full boot2qt image (and all packages) which you can flash to an SD card

```bash
# for Qt 6
bitbake b2qt-embedded-qt6-image

# for Qt 5
bitbake b2qt-embedded-qt5-image
```

The build process takes a long time, because it compiles all the source code. Output is like below for Qt 5:

```
BocDo@bocdo-virtual-machine:~/yocto/bq2t$ bitbake b2qt-embedded-qt5-image
Loading cache: 100% |#######################################################################################################################################################################| Time: 0:00:02
Loaded 4132 entries from dependency cache.
Parsing recipes: 100% |#####################################################################################################################################################################| Time: 0:00:01
Parsing of 2780 .bb files complete (2779 cached, 1 parsed). 4133 targets, 299 skipped, 1 masked, 0 errors.
NOTE: Resolving any missing task queue dependencies

Build Configuration:
BB_VERSION           = "1.44.0"
BUILD_SYS            = "x86_64-linux"
NATIVELSBSTRING      = "universal"
TARGET_SYS           = "arm-poky-linux-gnueabi"
MACHINE              = "raspberrypi4"
DISTRO               = "b2qt"
DISTRO_VERSION       = "3.0.4"
TUNE_FEATURES        = "arm vfp cortexa7 neon vfpv4 thumb callconvention-hard"
TARGET_FPU           = "hard"
SDKMACHINE           = "x86_64"
meta                 
meta-poky            = "HEAD:f2eb22a8783f1eecf99bd4042695bab920eed00e"
meta-raspberrypi     = "HEAD:0e05098853eea77032bff9cf81955679edd2f35d"
meta-oe              
meta-python          
meta-networking      
meta-initramfs       
meta-multimedia      = "HEAD:2b5dd1eb81cd08bc065bc76125f2856e9383e98b"
meta-python2         = "HEAD:4400f9155ec193d028208cf0c66aeed2ba2b00ab"
meta-boot2qt         
meta-boot2qt-distro  = "HEAD:5f23cb2d6836bbad3a1fa982089ccf20bf0c6245"
meta-mingw           = "HEAD:756963cc28ebc163df7d7f4b4ee004c18d3d3260"
meta-qt5             = "HEAD:72459ce0639eb3ce408558a7abede945e1f8ddc9"
meta-cluster         
workspace            = "<unknown>:<unknown>"

Initialising tasks: 100% |#########################################################################################################################| Time: 0:00:23
Sstate summary: Wanted 357 Found 325 Missed 32 Current 1877 (91% match, 98% complete)
NOTE: Executing Tasks
NOTE: Setscene tasks completed
Currently  1 running tasks (6901 of 6968)  99% #################################################################################################################  |
0: qtwebengine-5.15.2+gitAUTOINC+5537ff4437_fb6ab5e483-r0 do_compile (pid 213392)  74% |##################################################   
```
