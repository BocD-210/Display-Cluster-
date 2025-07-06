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


