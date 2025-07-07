# HassBeam – Setup

## Contents

- [Installation](#installation)
- [Capturing IR Commands](#capturing-ir-commands)
- [Home Assistant Setup](#home-assistant-setup)
  - [Creating Scripts to Send IR Commands](#creating-scripts-to-send-ir-commands)
  - [Creating a Dashboard to Control Devices](#creating-a-dashboard-to-control-devices)

<br>
<br>

---

## Installation

Clone this repository or download the latest release.

To install the the software on your ESP32, first make sure to configure your Wi-Fi credentials.  
A sample configuration is provided in the [esphome folder](esphome). You just need to rename the file `secrets_template.yaml` to `secrets.yaml` and adjust it with your local SSID and password.  
After that you can flash the provided configuration file `hassbeam.yaml` using [ESPHome](https://esphome.io/).  

While ESPHome can be used directly within Home Assistant, **compilation on limited hardware can be extremely slow**. It's recommended to install and use the ESPHome CLI on a PC for faster builds and flashing. You can find installation instructions in the [ESPHome Getting Started guide](https://esphome.io/guides/getting_started_command_line.html).

#### ESPHome CLI (recommended)

- connect the device via USB (if wifi is already configured you can also use OTA instead of USB)
- open a terminal in the esphome folder
- run `esphome run .\hassbeam.yaml`
- select the proper device
- The code should now be compiled and flashed to the device

#### ESPHome Dashboard (not recommended for most cases)

- [Physically Connecting to your Device](https://esphome.io/guides/physical_device_connection#physically-connecting-to-your-device)
- [Device Builder](https://esphome.io/guides/getting_started_hassio#device-builder-interface)
- copy the code inside `hassbeam.yaml` and flash it to the device

<br>
<br>

---

## Capturing IR Commands

**Many remote control Pronto codes can be found on remotecentral.com**

Received IR commands will trigger a Home Assistant event. You can obtain the IR code by subscribing to the event `esphome.hassbeam.ir_received`:
- Open Home Assistant
- go to Developer tools
- Click on events
- Paste the event id `esphome.hassbeam.ir_received` into the input field and subscribe to that event
- Point the remote at the sensor and press the button you want to capture
- The received command should appear in the Home Assistant Events
- Copy the command data of the desired buttons — these will be used later in Home Assistant to replicate the remote’s functionality.

*If the commands of a remote does not create an event, this could indicate that the protocol is not yet supported. You can find out which protocol is used by accessing the logs of your ESP32 and receiving the IR command.*
<br>
<br>

---

## Home Assistant Setup

Once the ESP device is connected to your network, it should automatically be discovered by Home Assistant via ESPHome integration. If not, add it manually using its IP address (use the ESPHome integration).
<br>
<br>

---

## Sending IR Commands

### Supported Protocols
The following protocols are currently supported:

| Protocol          | Service                   | Parameters                                                                            |
|------------------ |-------------------------- |-------------------------------------------------------------------------------------- |
| Ahea              | `send_ir_aeha`            | `address`: int<br>`data`: int[]                                                       |
| Beo4              | `send_ir_beo4`            | `source`: int<br>`command`: int                                                       |
| JVC               | `send_ir_jvc`             | `data`: int                                                                           |
| Haier             | `send_ir_haier`           | `code`: int[]                                                                         |
| LG                | `send_ir_lg`              | `data`: int<br>`nbits`: int #default=28                                               |
| NEC               | `send_ir_nec`             | `address`: int<br>`command`: int<br>`command_repeats`: int #default=1                 |
| Panasonic         | `send_ir_panasonic`       | `address`: int<br>`command`: int                                                      |
| Pioneer           | `send_ir_pioneer`         | `rc_code_1`: int<br>`rc_code_2`: int<br>`repeat_times`: int                           |
| Pioneer (simple)  | `send_ir_pioneer_simple`  | `rc_code_1`: int                                                                      |
| Pronto            | `send_ir_pronto`          | `data`: string                                                                        |
| Raw               | `send_ir_raw`             | `data`: string<br>`freq`: float                                                       |
| RC5               | `send_ir_rc5`             | `address`: int<br>`command`: int                                                      |
| RC6               | `send_ir_rc6`             | `address`: int<br>`command`: int                                                      |
| Roomba            | `send_ir_roomba`          | `data`: int<br>`repeat_times`: int # default 3<br>`wait_time_ms`: int # default 17ms  |
| Samsung           | `send_ir_samsung`         | `data`: int<br>`nbits`: int # default 32                                              |
| Samsung36         | `send_ir_samsung36`       | `address`: int<br>`command`: int                                                      |
| Sony              | `send_sony`               | `data`: int<br>`nbits`: int # default = 12                                            |
| Toshiba AC        | `send_toshiba_ac`         | `rc_code_1`: int<br>`rc_code_2`: int #optional                                        |

### Creating Scripts to Send IR Commands

*Since this part of the setup can be a bit tedious if you want to add a lot of buttons, a better integration into Home Assistant is planned.*

To send specific commands to your devices, you’ll need to define **scripts** in Home Assistant. Each script corresponds to a single button or action (e.g., "TV_Power", "AVR_Volume-Up").

Here's an example how to use the `send_ir_pronto` service in a Home Assistant script to send an IR command:
```yaml
 sequence:
  - action: esphome.hassbeam_send_ir_pronto
    metadata: {}
    data:
      data: 0000 006D 0022 0000 0157 00AA 0017 0015 0017 0015 0017 0015 0017 0015
        0017 0015 0017 0015 0017 0015 0017 0015 0017 003E 0017 003E 0017 003F 0016
        003F 0017 003F 0017 003F 0016 0015 0017 003F 0017 003F 0017 0015 0017 003F
        0017 0015 0017 003F 0016 0015 0016 0015 0016 0015 0016 0015 0016 003F 0016
        0015 0016 003F 0016 0015 0017 003F 0016 003F 0016 003F 0016 03C2 0000 006D
        0002 0000 0158 0056 0016 03C2
  alias: TV_OK
  description: 'Sends the IR command that corresponds with the OK-Button of the TVs remote'
}
```
This Example uses the Pronto Protocol but you can do this with the other supported protocols too. Make sure to use the [correct parameters](#supported-protocols) for the selected protocol. You can find more information and example parameters in the [ESPHome documentation](https://esphome.io/components/remote_transmitter.html#)


</br>

A sample configuration can be found in [`homeassistant/scripts.yaml`](./homeassistant/scripts.yaml). Replace the command data with your captured IR codes or create new scripts.  
You can add these scripts to Home Assistant by editing the scripts.yaml file (this method requires a restart) of your Home Assistant instance, or by adding it in the UI. If you want to add multiple Scripts at once, I recommend editing the scripts.yaml file.  


### Creating a Dashboard to Control Devices

You can create a user interface in Home Assistant to trigger these scripts easily. In the example setup, one dashboard page per remote is created (e.g., TV, AVR). The layout uses the [Mushroom](https://github.com/piitaya/lovelace-mushroom) custom cards from HACS for a clean and modern interface.

Example dashboard configurations are provided:

- [`dashboard_tv_example.yaml`](../homeassistant/dashboard_tv_example.yaml)
- [`dashboard_avr_example.yaml`](../homeassistant/dashboard_avr_example.yaml)

These can be imported or used as templates for your own setup.

![Dashboard Example](homeassistant/dashboard_example.png)
---
