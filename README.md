# SONOFF TX Ultimate Smart Touch Wall Switch

## Introduction
Step by step guide to use the switch in a WiFi + MQTT + Node-RED + Apple Home (HomeKit) environment without using SONOFF/itead software or servers.

- Consider backing up the original firmware before flashing anything (see below)
- You may use everything provided here for free (at your own risk)
- Did all of that using macOS Sonoma
- I ended up using tasmota instead of ESPHome because my HomeAssistant installation in Docker does not support addons :/

## Hardware
- Wall switch from itead.cc: [3 Gang, EU/86 model](https://itead.cc/product/sonoff-tx-ultimate-smart-touch-wall-switch/)
- USB Adapter from Amazon: [UART-TTL USB Adapter](https://www.amazon.de/dp/B08T24NML9)
- At least 3 hands (seriously, have someone help you with this)

## Setup

### Software
- Install esptools in terminal with `brew install esptool`
- Connect wires like explained here by [chris2172](https://github.com/chris2172/Sonoff-TX-Ultimate-T5-Switch)

![img](https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/blob/29abb2d71adfa2501f2e4c16085870247325c54d/wire1.jpg)

### Chip info
- I wanted to backup the firmware provided on the switch. I found a guide for this [here at hobbytronics.pk](https://hobbytronics.pk/sonoff-original-firmware-backup-restore/#google_vignette), but for older switches (with 1MB/4MB firmware). So needed a way to find out the proper size of the T5 firmware. Thanks to Claude 3.5 I came up with this chip-info command:
- In terminal run `esptool.py --port /dev/cu.usbserial-1440 flash_id`
- Result: The flash size is not 1MB or 4MB but 8MB

```
esptool.py v4.7.0
Serial port /dev/cu.usbserial-1440
Connecting....
Detecting chip type... Unsupported detection protocol, switching and trying again...
Connecting...
Detecting chip type... ESP32
Chip is ESP32-D0WDR2-V3-V3 (revision v3.0)
Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
Crystal is 40MHz
MAC: 54:43:b2:71:b9:2c
Uploading stub...
Running stub...
Stub running...
Manufacturer: 5e
Device: 4017
Detected flash size: 8MB
Hard resetting via RTS pin...
```

## Backup time
- With the help of my lovely wife I could easily plug in the UART-TTL USB adapter while holding a cable connected from the *ground pin* to the *BOOT* solder point (pin wire). The switch is now in *boot mode*.
- Running `esptool.py --port /dev/cu.usbserial-1440 read_flash 0x00000 0x800000 ~/Desktop/image8M.bin` takes some time but provided a nice firmware backup file I renamed properly afterwards: [sonoff-tx-ultimate-t5-3c-86-original-firmware-2024-06-28.bin](https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/blob/77f11c4167d40285a734aea873aa7d6982a4bdea/sonoff-tx-ultimate-t5-3c-86-original-firmware-2024-06-28.bin)
