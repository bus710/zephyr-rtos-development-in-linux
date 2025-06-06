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
    4. Samples
        a. Build the Hello World Sample and Flash the Sample
        b. Build the Blinky Sample and Flash the Sample
    5. (TBD) Get JLink
    6. (TBD) Wire the probe and the board
    7. (TBD) Debug the blinky app in VSCODE
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

Datasheets for Pico and Pico W:
- [Pico](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf)
- [Pico W](https://datasheets.raspberrypi.com/picow/pico-w-datasheet.pdf)

Zephyr Project Documentation (PDF)
- https://docs.zephyrproject.org/latest/zephyr.pdf

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
    picocom \
    minicom
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

## 4a. Build the Hello World Sample and Flash the Sample

west build -p always -b rpi_pico2/rp2350a/m33 -S cdc-acm-console samples/basic/blinky -- -DCONFIG_USB_DEVICE_INITIALIZE_AT_BOOT=y

Build the sample:
```sh
$ cd ~/zephyrproject/zephyr

# It returns related board names.
$ west boards -n pico 

# Build for the pico.
$ west build -p always -b rpi_pico/rp2040 \
    -S cdc-acm-console \
    samples/hello_world \
    -- -DCONFIG_USB_DEVICE_INITIALIZE_AT_BOOT=y

# Build for the pico w.
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

# or
$ minicom -b 115200 -o -D /dev/ttyACM0
```

<br/><br/>

## 4b. Build the Blinky Sample and Flash the Sample

Build the sample:
```sh
$ cd ~/zephyrproject/zephyr

# It returns related board names.
$ west boards -n pico 

# Build for the pico.
$ west build -p always -b rpi_pico/rp2040 samples/basic/blinky
```

Flash the sample into the target (make sure the devices is mounted as a disk drive):
```sh
$ west flash -r uf2
```

:warning: Pico W doesn't have a direct connection with the on board LED but the cyw43 is connected with the LED. So this blinky sample doesn't work for Pico W.

<br/><br/>










[Go to README](./README.md)
