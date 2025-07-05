---
title: "ESP32 Tutorial Series - Part 1 Setting up ESP-IDF"
seoTitle: "Setting up ESP-IDF for ESP32"
seoDescription: "We will install and setup ESP-IDF and look at creating projects and components, sdkconfig, and CMake files for ESP32"
datePublished: Thu Mar 17 2022 14:42:52 GMT+0000 (Coordinated Universal Time)
cuid: cl0v3qhjo00nej6nvdksj4g0y
slug: esp32-tutorial-series-part-1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1647527327001/neJ9QfJFS.jpg
tags: cpp, c, linux, iot, embedded

---

Hello and Welcome to part 1. 

For accessing to last parts, [click here](https://mohyfahim.ir/esp32-tutorial-series-p0).

In this part, we will install and setup ESP-IDF and look at creating projects and components, sdkconfig, and CMake files.

I am currently doing these steps on Linux, but these steps are repeatable on Windows and Mac. Our primary reference is [ESP-IDF documentation](https://docs.espressif.com/projects/esp-idf/en/latest/esp32). The items mentioned in this section are also fully described in this reference.
To use IDF there are several prerequisites (such as Python, Git, Cross compilers, etc) that you must have installed. If you are using Windows, there is an installation wizard that automates the installation process.
For Ubuntu or Debian you can use this command as follow: (  [read this for the rest of architectures](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/linux-macos-setup.html#get-started-prerequisites) )

```bash
$ sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
```
To install IDF, we first download its source from GitHub. You can download branches related to the specific releases, but I prefer to use the master branch.

```bash
$ mkdir -p ~/esp
$ cd ~/esp
$ git clone --recursive https://github.com/espressif/esp-idf.git
```
After downloading the files, go to the esp-idf folder and execute `./install.sh`. We can pass one or more specific version names ( or all ) to download the cross-compilation tools for them.
```bash
$ cd ~/esp/esp-idf
$ ./install.sh esp32,esp32s2
```
Now it’s time to export the `idf.py’s` path to use it in our project. You can make an alias to make exporting easy. Put this alias in your `.bashrc` or `.zshrc` file.
```bash
alias get_idf='. $HOME/esp/esp-idf/export.sh'
```
Now source that file
`source ~/.bashrc` or `source ~/.zshrc`. 
Finally, you can use `idf.py` without any issues.
You can now go to the folder where you want to create the project and use` idf.py `as follow:
```bash
$ get_idf # to load idf.py’s path
$ idf.py create-project tutorial
```
The project structure is as follow:
```bash
$ tree tutorial
├── CMakeLists.txt
└── main
    ├── CMakeLists.txt
    └── tutorial.c
```
The IDF structure is based on components. The project has the main component by default. You can add a new component by `idf.py create-component -C components component-name `command.
```bash
$ idf.py create-component -C components test-component
$ tree tutorial
├── CMakeLists.txt
├── components
│   └── test-component
│       ├── CMakeLists.txt
│       ├── include
│       │   └── test-component.h
│       └── test-component.c
└── main
    ├── CMakeLists.txt
    └── tutorial.c
```
The main component has a `tutorial.c` file that contains the program’s start point, the `app_main` function. You can use cpp instead of c, just add `extern “C”` before the `app_main`. I will cover this in the future.

The Cmake files contain scripts for building a project. In addition, such scripts exist in the IDF source, and we import them in the project's main CMake file to use the IDF components, ie `include($ENV{IDF_PATH}/tools/cmake/project.cmake)`. In order to add the components folder just created to the project, we add the following command in the main CMakeLists.txt file:
```bash
set(EXTRA_COMPONENTS_DIRS components/)
```
As a result, IDF will locate the components we created.

The `idf.py build` command builds the project. If you want to compile the code for a specific family, you must first use `idf.py set-target`, otherwise, esp32 is selected by default.
```bash
$ idf.py set-target esp32
$ idf.py build
```
In this step, after building the code, errors are shown. If the build steps are successful, the `idf.py flash -p port `command will flash the binary file onto the module.
```bash
$ idf.py flash -p /dev/ttyUSB0
```
The question might be, where did IDF store this code in this 4MB of flash memory? The answer is in the Partition Table. We have the ability to divide the flash memory into different sections and the IDF writes binary files including bootloader, partition table, app, etc to the appropriate address based on the partition table we gave it. It uses `esptool` for flashing purposes. This tool has interesting features that we'll mention if necessary. By default, there are several partition tables that we can use or write a custom one. But where and how to make these settings?

With the `idf.py menuconfig` command, we can set a number of settings. 

![Screenshot from 2022-03-17 17-54-26.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647527494408/o85_xMa3r.png)

You can see different settings for different sections. We will survey this command in the next part. The output of this menu command is in the `sdkconfig` file, which is used during the compilation process. Our future components will have a section in the menuconfig so that we can systematically change their settings.
If you run the above commands without any problems, your idf is ready and you can enter the next step. If there is a particular problem, share it so we can investigate.

Good luck.

