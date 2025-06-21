# ESPHome Universal Remote
A universal remote that can be integrated into Home Assistant using ESPHome.

## Contents
- [Requirements](#requirements)
- [Hardware](#hardware)
- [Setup](#setup)
- [Related projects](#related-projects)

## Requirements


To use this project, you will need a working [Home Assistant](https://www.home-assistant.io/) instance, as the universal remote integrates directly with it. The core of the device is an ESP32 microcontroller, which runs the logic and communicates with Home Assistant via [ESPHome](https://esphome.io/).

In addition to the ESP32, a few electronic components are required — see the [Parts](hardware.md#parts) section for details. 

Optionally, design files for a custom PCB or perfboard layout are provided, along with a 3D-printable case for the device.


## Getting Started

To get started with the ESPHome Universal Remote, you will need to assemble the necessary hardware and flash the ESP32 with the appropriate configuration using ESPHome.

### Hardware

An overview of the required components — including the ESP32, IR hardware, and optional PCB or 3D-printed case — can be found in the [Hardware](hardware.md) section. This includes a detailed [Parts](hardware.md#parts) list and links to design resources.

### Setup

Once your hardware is ready, follow the instructions in the [Setup](setup.md) section to install ESPHome, flash the firmware onto your ESP32, and integrate the remote with your Home Assistant instance. 
The setup guide also explains how to capture IR signals from existing remotes and how to send them using the ESP32.

## Related Projects

Check out these projects that helped in the creation of this project:
- [ESPHome](https://github.com/esphome/esphome)  
  Used to configure and flash the ESP32. Also handles IR signal sending and receiving.

- [Home Assistant](https://github.com/home-assistant/core)  
  Serves as the central control hub for triggering IR commands and integrating the remote into smart home automations.

- [Mushroom UI](https://github.com/piitaya/lovelace-mushroom)  
  A modern and customizable UI component library for Home Assistant dashboards, used in the example configuration to create clean remote interfaces.

- [Fritzing](https://github.com/fritzing/fritzing-app)  
  Used to create the wiring diagrams and PCB layout.
