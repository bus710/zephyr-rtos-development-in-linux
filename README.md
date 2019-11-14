# Zephyr RTOS Development in Linux

Zephyr is an RTOS for IoT projects.  
This repo is a walkthrough to prepare Zephyr development environment for nRF52 devices in Ubuntu.

<br/><br/>

## Index

- Prerequisites
- Reference
- Contents
    1. Linux dependencies
    2. Get zephyr source code and tools
    3. Get zephyr SDK
    4. Build the blinky application for nRF52832-DK
- What's next 

<br/><br/>

## Prerequisites

- nRF52832-DK board
- Ubuntu 19.10 or newer version on a PC

<br/><br/>

## Reference

- [nRF development in linux](https://github.com/bus710/nrf-development-in-linux)
- [Home page](https://zephyrproject.org)
- [Documents](https://docs.zephyrproject.org/latest/)
- [Introduction](https://docs.zephyrproject.org/latest/introduction/index.html#project-resources)
- [Downloads](https://www.zephyrproject.org/developers/#downloads)
- [Linux dependencies](https://docs.zephyrproject.org/latest/getting_started/installation_linux.html#installation-linux)
- [Getting started](https://docs.zephyrproject.org/latest/getting_started/index.html)
- [Beyond the getting started](https://docs.zephyrproject.org/latest/guides/beyond-GSG.html#beyond-gsg)
- [West - zephyr meta tool](https://docs.zephyrproject.org/latest/guides/west/index.html#west)
- [Samples and demos](https://docs.zephyrproject.org/latest/samples/index.html#samples-and-demos)
- [Application development](https://docs.zephyrproject.org/latest/application/index.html#application)
- [Overview: nRF52832 + zephyr](https://docs.zephyrproject.org/latest/boards/arm/nrf52_pca10040/doc/index.html)
- [JLink for nRF52](https://docs.zephyrproject.org/latest/guides/tools/nordic_segger.html#nordic-segger-flashing)

Please install the nRF SDK and tools based on the first link and follow the steps in this doc.

<br/><br/>

---

## 1. Linux dependencies

Install some packages:

```
$ sudo apt-get install --no-install-recommends \
    git cmake ninja-build gperf \
    ccache dfu-util device-tree-compiler wget \
    python3-pip python3-setuptools python3-tk python3-wheel \
    xz-utils file make gcc gcc-multilib
```

Check the versions (should be newer than these):

```
$ cmake --version

cmake version 3.13.4

$ dtc --version

Version: DTC 1.4.7

$ pip3 --version

pip 18.1 from /usr/lib/python3/dist-packages/pip (python 3.7)
```

Get west:

```
$ pip3 install --user -U west
$ echo 'export PATH=~/.local/bin:"$PATH"' >> ~/.bashrc
$ source ~/.bashrc
$ west --version

West version: v0.6.3
```
<br/><br/>

## 2. Get zephyr source code and tools

Here each step downloads many files from the internet:
- the source code in west initializing is about 600~700MB
- the objects in west updating is about 300MB
- the packages in pip3 installing is about 100MB

```
$ cd ~
$ west init zephyrproject
$ cd zephyrproject
$ west update
$ pip3 install --user -r ~/zephyrproject/zephyr/scripts/requirements.txt
```

<br/><br/>

## 3. Get zephyr SDK

Check the latest stable SDK version first:
- https://www.zephyrproject.org/developers/#downloads
- 0.10.3 is the latest as of today
- The size is about 1.1GB
 
Download it:

```
$ wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.10.3/zephyr-sdk-0.10.3-setup.run
```

Run the SDK installer:

```
$ chmod 744 zephyr-sdk-0.10.3-setup.run
# ./zephyr-sdk-0.10.3-setup.run -- -d ~/zephyr-sdk-0.10.3
```

Since the extracted files are stored in **~/zephyr-sdk-0.10.3**, put these in bashrc or so:

```
export ZEPHYR_TOOLCHAIN_VARIANT=zephyr
export ZEPHYR_SDK_INSTALL_DIR=$HOME/zephyr-sdk-0.10.3
```

To apply the variables:

```
$ source ~/.bashrc
```

Get an udev rule file for oopenocd:

```
$ sudo cp ${ZEPHYR_SDK_INSTALL_DIR}/sysroots/x86_64-pokysdk-linux/usr/share/openocd/contrib/60-openocd.rules /etc/udev/rules.d

$ sudo udevadm control --reload
```

<br/><br/>

## 4. Build the blinky application

Add this to bashrc:

```
source ~/zephyrproject/zephyr/zephyr-env.sh
```

Apply the variables:

```
$ source ~/.bashrc
```

Then build:

```
$ west build -p auto -b nrf52_pca10040 samples/basic/blinky
```

<br/><br/>

---

## What's next

TBD