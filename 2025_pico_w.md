[Go to README](./README.md)

<br/>


# Zephyr RTOS Development in Linux

Zephyr is an RTOS for IoT projects.  
This repo is a walkthrough to prepare Zephyr development environment for Raspberry Pico W (rp2040 + cyw43) device in Debian/Ubuntu.

<br/><br/>

## Index

- Prerequisites
- Reference
- Contents
    1. Install dependencies
    2. Get Zephyr and install Python dependencies
    3. Install the Zephyr SDK
    4. Build the Blinky Sample and Flash the Sample
    5. Get JLink
    6. Wire the probe and the board
    7. Debug the blinky app in VSCODE
- What's next 

<br/><br/>

## Prerequisites

- Pico W or Pico 2 W
- Debian testing or Ubuntu 25.04 on a PC
- JLink (nice to have for debugging)

<br/><br/>

## Reference

- [Home page](https://zephyrproject.org)
- [Documents](https://docs.zephyrproject.org/latest/)
- [Introduction](https://docs.zephyrproject.org/latest/introduction/index.html#project-resources)
- [Getting started](https://docs.zephyrproject.org/latest/getting_started/index.html)
- [Beyond the getting started](https://docs.zephyrproject.org/latest/guides/beyond-GSG.html#beyond-gsg)
- [West - zephyr meta tool](https://docs.zephyrproject.org/latest/guides/west/index.html#west)
- [Samples and demos](https://docs.zephyrproject.org/latest/samples/index.html#samples-and-demos)
- [Application development](https://docs.zephyrproject.org/latest/application/index.html#application)
- [Supported boards](https://docs.zephyrproject.org/latest/boards/index.html#)
- [Rpi pico](https://docs.zephyrproject.org/latest/boards/raspberrypi/rpi_pico/doc/index.html)
- [Detailed explanation for debuggers](https://interrupt.memfault.com/blog/a-deep-dive-into-arm-cortex-m-debug-interfaces)
- [Hackster.io tutorial part 1](https://www.hackster.io/cdwilson/zephyr-rtos-on-raspberry-pi-pico-2-part-1-cf39f0)

Datasheets:
- [Pico](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf)
- [Pico W](https://datasheets.raspberrypi.com/picow/pico-w-datasheet.pdf)


<br/><br/>

---

## 1. Install dependencies

Install some packages:
```sh
$ sudo apt update
$ sudo apt install --no-install-recommends \
    git cmake ninja-build gperf \
    ccache dfu-util device-tree-compiler wget \
    python3-dev python3-pip python3-setuptools python3-tk python3-wheel \
    xz-utils file make gcc gcc-multilib \
    g++-multilib libsdl2-dev libmagic1

$ sudo apt install -y \
    picocom
```

Check the versions (should be newer than these):
```sh
$ cmake --version
cmake version 3.31.6

$ python3 --version
Python 3.13.2

$ dtc --version
Version: DTC 1.7.2
```

<br/><br/>

## 2. Get Zephyr and install Python dependencies

```sh
$ sudo apt install python3-venv
$ python3 -m venv ~/zephyrproject/.venv

# This should be called per new terminal
$ source ~/zephyrproject/.venv/bin/activate 

$ pip install west
$ west init ~/zephyrproject
$ cd ~/zephyrproject
$ west update
$ west zephyr-export
$ west packages pip --install
```

Add an alias for the venv activation in `bashrc` or `zshrc`:
```sh
alias za="source ~/zephyrproject/.venv/bin/activate"
```

<br/><br/>

## 3. Install the Zephyr SDK

Install the 32 bit ARM compiler and tools:
```sh
$ cd ~/zephyrproject/zephyr
$ west sdk install \
    -d ~/zephyrproject/sdk \
    -t arm-zephyr-eabi
```

<br/><br/>

## 4. Build the Blinky Sample and Flash the Sample

Build the sample:
```sh
$ cd ~/zephyrproject/zephyr

# It returns related board names.
$ west boards -n pico 

# Build with the target MCU specified.
$ west build -p always -b rpi_pico/rp2040 samples/basic/blinky

# pico and pico w have different LED port connections.
# pico uses the GPIO 25
# pico w uses the cyw43's pin to control the LED, so this example doesn't work.
```

Flash the sample into the target (make sure the devices is mounted as a disk drive):
```sh
$ west flash -r uf2
```

### If the `west flash` doesn't work...

What is happening here is, there is no JTAG probe connected between the host and target, so OpenOCD cannot find the target.

For now, we can do the usual drag-drop method:
- The image file is created in `$HOME/zephyrproject/zephyr/build/zephyr/zephyr.uf2`. 
- Unplug the board, press the reset button, plug it again to the host.
- The board will appear as `RPI-RP2` drive. Mount it.
- Drag the uf2 file from a file browser and drop it in the `RPI-RP2` disk.


<br/><br/>

## 4b. Build the Hello World Sample and Flash the Sample

west build -p always -b rpi_pico2/rp2350a/m33 -S cdc-acm-console samples/basic/blinky -- -DCONFIG_USB_DEVICE_INITIALIZE_AT_BOOT=y

Build the sample:
```sh
$ cd ~/zephyrproject/zephyr

# It returns related board names.
$ west boards -n pico 

# Build with the target MCU specified.
$ west build -p always -b rpi_pico/rp2040/w \
    -S cdc-acm-console \
    samples/hello_world \
    -- -DCONFIG_USB_DEVICE_INITIALIZE_AT_BOOT=y
```

Flash the sample into the target (make sure the devices is mounted as a disk drive):
```sh
$ west flash -r uf2
```

Check the output:
```sh
$ picocom /dev/ttyACM0
```






<br/><br/>

The contents below are not yet completed.  
Please ignore.  

<br/><br/>




## 5. Get JLink package

We can download some software tools from Nordic semiconductor:
- JLinkExe is required to use JLink
- nrfjprog is specifically requried to work with nRF chips

Download nRF5x-Command-Line-Tools for Linux 64 bit from:
- https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Command-Line-Tools/Download#infotabs
- (if the above link doesn't work) https://www.nordicsemi.com > Software and Tools > Development tools > nRF command line tools. Please pick the OS (Linux 64) and download.

Run these commands to install the tools:

```
$ tar xvf nRF-Command-Line-Tools_10_4_1_Linux-amd64.tar.gz
$ sudo dpkg -i JLink_Linux_V650b_x86_64.deb
$ sudo dpkg -i nRF-Command-Line-Tools_10_4_1_Linux-amd64.deb
```

To check the version of installed tools:

```
$ nrfjprog -v

nrfjprog version: 10.4.1 
JLinkARM.dll version: 6.50b

$ mergehex -v

mergehex version: 10.4.1

$ JLinkExe -v

SEGGER J-Link Commander V6.50b (Compiled Sep  6 2019 17:46:52)
DLL version V6.50b, compiled Sep  6 2019 17:46:40

Unknown command line option -h. (<= Don't worry about this)
```

<br/><br/>

## 6. Wire the probe and the board

<br/><br/>

## 7. Debug the blinky app in VSCODE

First, install VSCODE:
- https://code.visualstudio.com/download
- Don't forget to install **Cortex-Debug** and **C/C++** extension

Open the blinky app in VSCODE: 

```
$ cd ~/zephyrproject/zephyr/samples/basic/blinky
$ code .
```

To create the launch.json file in VSCODE,
- press **CTRL + SHIFT + P** 
- use the **Debug: Open launch.json**
- choose the **Cortex-Debug**
- follow one of the below options

<br/><br/>

### 6.1 For Jlink GDB host and the on board probe

Paste this for the launch.json for Cortex-Debug extension:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Zephyr nRF52832",
            "cwd": "${workspaceRoot}",
            "executable": "build/zephyr/zephyr.elf",
            "request": "launch",
            "type": "cortex-debug",
            "servertype": "jlink",
            "device": "nrf52832_xxaa",
            "targetId": "nrf52",
            "boardId": "",
            "armToolchainPath": "${HOME}/zephyr-sdk-0.10.3/arm-zephyr-eabi/bin",
            "interface": "swd",
            "gdbpath": "/usr/bin/gdb-multiarch",
        },
    ]
}
```

To start debugging, press the F5 key twice.  
However, this may not support RTOS-awareness.

<br/><br/>

### 6.2 For Openocd GDB host and the on board probe

Paste this for the launch.json for Cortex-Debug extension:

```json
{
    "version": "0.2.0",
    "configurations": [

        {
            "name": "Zephyr nRF52832 openocd",
            "cwd": "${workspaceRoot}",
            "executable": "build/zephyr/zephyr.elf",
            "request": "launch",
            "type": "cortex-debug",
            "servertype": "openocd",
            "device": "nrf52832_xxaa",
            "targetId": "nrf52",
            "boardId": "",
            "armToolchainPath": "${HOME}/zephyr-sdk-0.10.3/arm-zephyr-eabi/bin",
            "interface": "swd",
            "gdbpath": "/usr/bin/gdb-multiarch",
             "configFiles": [
                 "/usr/share/openocd/scripts/board/nordic_nrf52_dk.cfg"
            ]
        },
    ]
}
```

To start debugging, press the F5 key twice.  

<br/><br/>

---

## What's next

The application doc introduces the basic work flow:
- https://docs.zephyrproject.org/latest/application/index.html#

In the same doc, there is a section that describes to make a custom system:
- https://docs.zephyrproject.org/latest/application/index.html#custom-board-devicetree-and-soc-definitions
  
Also, there are tips in the user and developer guide:
- https://docs.zephyrproject.org/latest/guides/index.html

Good luck!

<br/>

[Go to README](./README.md)
