---
title: "ESP32 tutorial Series Based on ESP-IDF"
seoTitle: "ESP32 tutorial Series Based on ESP-IDF"
seoDescription: "ESP32 Tutorial on CSI and RSSI of WiFi using ESP-IDF APIs."
datePublished: Wed Mar 16 2022 19:51:18 GMT+0000 (Coordinated Universal Time)
cuid: cl0tzbaso00bviknvg0sz46hg
slug: esp32-tutorial-series-p0
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/jXd2FSvcRr8/upload/v1647458937209/07B56xP-K.jpeg
tags: cpp, c, iot, embedded, internet-of-things

---

In this series, I am going to talk about using the ESP32 module. You will find good and enough information about setting up this module using the Arduino IDE on ...

# Table of Contents
This will be updated

- [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [ESP32 introduction](#esp32-introduction)

# Introduction
Hello and welcome.

In this series, I am going to talk about using the ESP32 module. You will find good and enough information about setting up this module using the Arduino IDE on the Web. In addition, using the Arduino IDE makes a lot of things easier, but the core used for ESP32 is the ESP-IDF compiled.

The [ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html) is the espressif framework for ESP32 modules and their families (ESP32s2, ESP32s3, ESP32c3 and etc). If you check their [GitHub](https://github.com/espressif/esp-idf), you will notice newly updated. However, the [Arduino IDE Core](https://github.com/espressif/arduino-esp32) is based on the specific ESP-IDF releases, so it doesn't update simultaneously with IDF. On the other hand, some configuration parameters (such as buffer sizes, etc.) were fixed when compiling the ESP-IDF source code. 
These limit your ability to configure and maneuver embedded systems.

In my case, I had to use the ESP-IDF instead of Arduino IDE, so I'm going to share with you what I learned about setting up the ESP-IDF in this series.

I would appreciate it if you would share your knowledge about this with me and if you found this content helpful, please share it with your friends as well. 

# ESP32 introduction

The ESP32 module is an SoC microcontroller that incorporates WiFi and Bluetooth. It is available in single-core and dual-core versions. These processors belong to the Xtensa family or RISC-V. The RAM and flash storage also differ according to the different versions.

[Hardware Comparision](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/chip-series-comparison.html)

I use ESP32-CAM, which is based on ESP32-s from AiThinker. This module has a 2MP camera and a 4MB PSRAM and FLASH memory. There is no specific difference between their core ( ESP32 and ESP32-CAM ), so we choose the target to esp32 when compiling codes. Some variants have external PSRAM inside, while some do not. While some use the onboard PCB antenna, we can swap it out for an external antenna. We will discuss this in the future. I plan to talk about the following subjects for now based on what I have in mind:

- Setting up the ESP-IDF
- WiFi and  provisioning
- Bluetooth
- HTTP client and server
- Socket client and server
- MQTT client
- ESP-NOW
- File systems and other storage
- Camera
- Mesh Network
- Face Detection via CNN
- WiFi CSI

Whenever you find something interesting or useful, tell me about it so we can check it out.

Thanks.

