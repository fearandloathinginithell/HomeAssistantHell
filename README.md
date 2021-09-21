# Home Assistant

## Hardware
* [ODROID-N2+ with 4GByte RAM](https://www.hardkernel.com/shop/odroid-n2-with-4gbyte-ram-2/)
* [128GB eMMC Module N2 Linux](https://www.hardkernel.com/shop/128gb-emmc-module-n2-linux-2/)
* [USB3.0 eMMC Module Writer](https://www.hardkernel.com/shop/usb3-0-emmc-module-writer/) used to flash Home Assistant onto the eMMC module.
### DD7002B WIFI Bridge
* [EZSync010](https://purenitetech.com/product/ezsync010/)
* [WIFI Bridge DD7002B](https://www.ismartwindow.co.nz/DD7002B_WIFI_bridge.html)
### Zigbee
* [Zigbee USB gateway ConBee II](https://www.phoscon.de/en/conbee2)
* [Philips Hue](https://www.philips-hue.com/en-nz)

## Home Assistant Glow 🌟
Waiting on Hardware

https://github.com/klaasnicolaas/home-assistant-glow

## DD7002B WIFI Bridge
### Requirements
* DD7002B WIFI Bridge firmware 0.8.0 or greater
* EZSync010 or other RS485 bridge

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
```
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
```
cover.blind_lounge_se:
  icon: mdi:blinds
cover.bedroom_*:
  icon: mdi:blinds
```
## Shellies Discovery
Migrated to native Shelly Intergration

https://github.com/bieniu/ha-shellies-discovery
