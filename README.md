# Home Assistant
I'm home grown in ![NZ Flag](https://github.com/oxguy3/flags/blob/master/mini/nz.png) You'll generally see NZ links. I enjoy tinkering in Home Assistant in my down time. If you find something useful in here, Yay!

## Hardware
* [ODROID-N2+ with 4GByte RAM](https://www.hardkernel.com/shop/odroid-n2-with-4gbyte-ram-2/)
* [128GB eMMC Module N2 Linux](https://www.hardkernel.com/shop/128gb-emmc-module-n2-linux-2/)
* [USB3.0 eMMC Module Writer](https://www.hardkernel.com/shop/usb3-0-emmc-module-writer/) used to flash Home Assistant onto the eMMC module.
### DD7002B WIFI Bridge
* [EZSync010](https://purenitetech.com/product/ezsync010/)
* [WIFI Bridge DD7002B](https://www.ismartwindow.co.nz/DD7002B_WIFI_bridge.html)
### OpenSprinkler
* [OpenSprinkler OS3.0 AC-Powered](https://opensprinkler.com/product/opensprinkler/)
* [Ring AC Power Adapter](https://ring.com/au/en/products/video-doorbell-plugin-adapter-gen-2) I'm actually using the first generation power adapter for a 'Ring Video Doorbell Pro'
* [Pope 25mm Solenoid Valve With Flow Control](https://www.popeproducts.com.au/irrigation/automated-control-and-accessories/valves/25-mm-solenoid-valve-with-flow-control)

### Shelly
* [Shelly 1](https://shelly.cloud/products/shelly-1-smart-home-automation-relay/)
* [Shelly 1PM](https://shelly.cloud/products/shelly-1pm-smart-home-automation-relay/)
* [Shelly 2.5](https://shelly.cloud/products/shelly-25-smart-home-automation-relay/)
* [Shelly Dimmer 2](https://shelly.cloud/products/shelly-dimmer-2-smart-home-light-controller/)
* [Shelly Button1](https://shelly.cloud/products/shelly-button-1-smart-home-automation-device/)
### Zigbee
* [Zigbee USB gateway ConBee II](https://www.phoscon.de/en/conbee2)
* [Philips Hue](https://www.philips-hue.com/en-nz)

## Home Assistant Glow 🌟
Additonal components:
* [IP65 Sealed ABS Enclosure](https://www.jaycar.co.nz/ip65-sealed-abs-enclosures-dark-grey-with-mounting-flange-64x58x35mm/p/HB6121)
* [Vented ABS Enclosure](https://www.jaycar.co.nz/grey-vented-abs-enclosure-40-x-40-x-20mm/p/HB6114)

Including the hardware discussed here:
https://github.com/klaasnicolaas/home-assistant-glow
No additonal changes made to Klaas's configuration
## DD7002B WIFI Bridge
### Requirements
* DD7002B WIFI Bridge firmware 0.8.0 or greater
* EZSync010 or other RS485 bridge
#### Wiring EZSync010 to DD7002B, RS485 Interface
EZsync010 was terminated to a RJ9/4P4C plug

| EZSync010 | RJ9/4P4C | Function |
| ------------- | ------------- | ------------- |
| Black | 1 | Ground |
| - | 2 | - |
| Orange | 3 | 485A |
| Yellow | 4 | 485B |
| Red | - | Power |
| Brown | - | Terminator 1 |
| Green | - | Terminator 2 |

'-' denotes not in use

Further Documentation:

* [DD7002B](/Assets/README/DD7002B_Docs)
* [EZSync010](/Assets/README/EZsync010_Docs)


![EZSync010](/Assets/README/EZSync010.jpeg)

### Drivers
#### macOS Catalina
No additional drivers required
#### Windows 10 1909
Drivers for chipset https://www.ftdichip.com/FTDrivers.htm

#### Raspberry Pi
No additional drivers required

#### ODROID-N2+
No additional drivers required

### Connecting to Bridge Raspberry Pi
Install Screen if required
```Shell
sudo apt-get install screen
```
```Shell
screen /dev/ttyUSB0 9600
```
### Connecting to Bridge macOS Catalina
List USB devices to retreive the name of the EZSync010

```Shell
ls /dev/tty.*
/dev/tty.Bluetooth-Incoming-Port	/dev/tty.usbserial-AB0KFHEJ
```
Connect to EZSync010 and specify the baud rate
```Shell
screen /dev/tty.usbserial-AB0KFHEJ 9600
```
### Issue Commands
#### Query Bridge
```Shell
!123D000v?;
```
##### Response
```Shell
!123D001vZ10;!123D002vZ10;!123D003vU10;!123DFFFv000;
```
### Blind information
| Value  | Description |
| ------------- | ------------- |
| !123D001vZ10  | Dining Room 1, One Way Blind |
| !123D002vZ10  | Dining Room 2, One Way Blind |
| !123D003vU10  | Kitchen Blind, Two Way Blind |
| !123DFFFv000  | Unknown |

### Home Assistant
Working Home Assistant configuration

![iOS_blinds](/Assets/README/iOS_Blinds2.png)
#### configuration.yaml
https://www.home-assistant.io/integrations/cover.command_line/
```yaml
default_config:
homeassistant:
  customize_glob: !include customize_glob.yaml
cover:
  - platform: command_line
  # One way blinds
    covers:
      blind_lounge_se:
        command_open: '/bin/bash -c "( stty raw speed 9600 >&2; echo -ne ''!123D005o;'' ) >/dev/ttyUSB0 <&1"'
        command_close: '/bin/bash -c "( stty raw speed 9600 >&2; echo -ne ''!123D005c;'' ) >/dev/ttyUSB0 <&1"'
        command_stop: '/bin/bash -c "( stty raw speed 9600 >&2; echo -ne ''!123D005s;'' ) >/dev/ttyUSB0 <&1"'
        friendly_name: Lounge South East
      blind_bedroom_master:
        command_open: '/bin/bash -c "( stty raw speed 9600 >&2; echo -ne ''!123D006o;'' ) >/dev/ttyUSB0 <&1"'
        command_close: '/bin/bash -c "( stty raw speed 9600 >&2; echo -ne ''!123D006c;'' ) >/dev/ttyUSB0 <&1"'
        command_stop: '/bin/bash -c "( stty raw speed 9600 >&2; echo -ne ''!123D006s;'' ) >/dev/ttyUSB0 <&1"'
        friendly_name: Bedroom Master
```
#### customize_glob.yaml
```yaml
cover.blind_lounge_se:
  icon: mdi:blinds
cover.bedroom_*:
  icon: mdi:blinds
```
## Shelly

Shelly API Documentation https://shelly-api-docs.shelly.cloud/

Shelly Gen 1 Firmware Change Log https://shelly-api-docs.shelly.cloud/gen1/#changelog

Shelly Gen 2 Firmware Change Log https://shelly-api-docs.shelly.cloud/gen2/changelog
### Shellies Discovery
Migrated to native Shelly Intergration
https://github.com/bieniu/ha-shellies-discovery
### CoIoT
Be sure to configure Home Assistant IP address and port don't use Multicast

![CoIoT Settings](/Assets/README/CoLot.png)