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
- Running macOS I easily used [Homebrew](https://brew.sh/) to install the esptools via the terminal
- `brew install esptool`
- Connect wires like explained here by [chris2172](https://github.com/chris2172/Sonoff-TX-Ultimate-T5-Switch)

![img](https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/blob/29abb2d71adfa2501f2e4c16085870247325c54d/wire1.jpg)

### Chip info
- I wanted to backup the firmware provided on the switch. I found a guide for this [here at hobbytronics.pk](https://hobbytronics.pk/sonoff-original-firmware-backup-restore/#google_vignette), but for older switches (with 1MB/4MB firmware). So needed a way to find out the proper size of the T5 firmware. Thanks to Claude 3.5 I came up with this chip-info command:
- `esptool.py --port /dev/cu.usbserial-1440 flash_id`
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
- With the help of my lovely wife ❤️ I could easily plug in the UART-TTL USB adapter while holding a cable connected from the *GROUND PIN* to the *BOOT* solder point (pink wire). The switch is now in *boot mode*. So we can run the following terminal command:
- `esptool.py --port /dev/cu.usbserial-1440 read_flash 0x00000 0x800000 ~/Desktop/image8M.bin`
- This took a couple of minutes but provided a nice firmware backup file I renamed properly afterwards: [sonoff-tx-ultimate-t5-3c-86-original-firmware-2024-06-28.bin](https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/blob/77f11c4167d40285a734aea873aa7d6982a4bdea/sonoff-tx-ultimate-t5-3c-86-original-firmware-2024-06-28.bin)
- Note: You can relieve the cramp in (your?) third hand a little by removing the pink wire once the reading has started and you are sure everything works.

## Flashing
- I decided to use the web installer route for installing tasmota so I did not have to download the proper firmware from a selection of 100s of variations and get URLs, files, paths and stuff right. The [web installer](https://tasmota.github.io/docs/Getting-Started/#flashing) does all that for me.
- First I unplugged the USB adapter again, reapplied the pink wire (thanks again, ❤️) and plugged the USB adapter in again.
- Then I flashed the firmware by following the nicely guided web assistant.

<div style="text-align:left;">
  <img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/389aa5e5-7ad8-4945-82d8-d51c56b7ad54" width="50%" style="border:1px solid #444; margin-bottom:20px;" alt="Screenshot 2024-06-29 at 09 40 36">

  <img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/732a790d-8e36-4761-baa8-5ae720f36dd3" width="50%" style="border:1px solid #444; margin-bottom:20px;" alt="Screenshot 2024-06-29 at 09 41 25">

  <img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/e63534fb-d8ba-4c71-90b9-8701d555d326" width="50%" style="border:1px solid #444; margin-bottom:20px;" alt="Screenshot 2024-06-29 at 09 41 42">

  <img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/7c9cdcfc-c284-40ee-97c6-0abf647ff872" width="50%" style="border:1px solid #444; margin-bottom:20px;" alt="Screenshot 2024-06-29 at 09 41 50">

  <img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/f03162a9-0642-406e-9844-ca80a185c276" width="50%" style="border:1px solid #444; margin-bottom:20px;" alt="Screenshot 2024-06-29 at 09 42 00">

  <img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/2c0ea299-cdb0-4244-89c2-2a937672728c" width="50%" style="border:1px solid #444; margin-bottom:20px;" alt="Screenshot 2024-06-29 at 09 42 25">
</div>

## First contact
- Tasmota then is installed and creates a WiFi to connect with.
- Connect to this tasmota-* WiFi (there are plenty of guides for that online so not covered here) and go to a web browser if one does not pop up automatically (it should) and (in my case) go to 192.168.4.1.
- You are asked to enter SSID for your home network.
