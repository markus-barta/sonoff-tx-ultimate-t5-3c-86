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

### Wiring

- The switch consists of three main components:
  1. The high-voltage back section containing the relays
  2. The logic board section housing the ESP32 microcontroller
  3. The removable white front plate, which is the user interface for daily operation
- Caution: Before proceeding, shut off the power supply. Then disconnect all wiring from the high-voltage section. This is for safety precautions, even though we won't be working on this part. For flashing, we only need the logic board section. The high-voltage section and front plate are not required for this process.
- Disassembly steps:
  1. Separate the logic board section from the high-voltage back section.
  2. (Optional) Remove the front plate if it's attached to prevent scratching during work.
  3. Carefully open the logic board section:
     a. Locate and remove the four small screws securing the cover.
     b. Gently lift the cover to expose the logic board.
- Connect the wires to your _UART-TTL USB Adapter_ like explained here by [chris2172](https://github.com/chris2172/Sonoff-TX-Ultimate-T5-Switch)

<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/blob/29abb2d71adfa2501f2e4c16085870247325c54d/wire1.jpg" width="25%" height="25%" alt="wire1.jpg" onclick="window.open(this.src, '_blank');">

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

<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/389aa5e5-7ad8-4945-82d8-d51c56b7ad54" width="25%" height="25%" alt="Screenshot 2024-06-29 at 09 40 36" onclick="window.open(this.src, '_blank');">
---
<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/732a790d-8e36-4761-baa8-5ae720f36dd3" width="25%" height="25%" alt="Screenshot 2024-06-29 at 09 41 25" onclick="window.open(this.src, '_blank');">
---
<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/e63534fb-d8ba-4c71-90b9-8701d555d326" width="25%" height="25%" alt="Screenshot 2024-06-29 at 09 41 42" onclick="window.open(this.src, '_blank');">
---
<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/7c9cdcfc-c284-40ee-97c6-0abf647ff872" width="25%" height="25%" alt="Screenshot 2024-06-29 at 09 41 50" onclick="window.open(this.src, '_blank');">
---
<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/f03162a9-0642-406e-9844-ca80a185c276" width="25%" height="25%" alt="Screenshot 2024-06-29 at 09 42 00" onclick="window.open(this.src, '_blank');">
---
<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/2c0ea299-cdb0-4244-89c2-2a937672728c" width="25%" height="25%" alt="Screenshot 2024-06-29 at 09 42 25" onclick="window.open(this.src, '_blank');">

## First contact
- After flashing Tasmota, we want to connect the switch to your home network
- The device will create its own WiFi access point named "tasmota-XXXXXX" where XXXXXX is a unique identifier. Connect to this "tasmota-XXXXXX" network
- Once connected, you should automatically be redirected you to the Tasmota configuration page (usually at http://192.168.4.1). If not, open a web browser and navigate to this address manually.
- On the configuration page, you'll see a "Configure WiFi" section. Enter the SSID and the password for your home WiFi in the second field. Click "Save" to apply these settings.
- The switch will restart and attempt to connect to your home network. If successful, the switch will disappear from the available WiFi networks list. If not, it will revert to Access Point mode after a few minutes, allowing you to try the configuration process again.
- To find your switch on your home network you need it's IP. Check your router's connected devices list for a new device i.e. the switch named something with "tasmota". For that I use [LanScan](https://apps.apple.com/at/app/lanscan/id472226235%3Fmt%3D12&ved=2ahUKEwj5w8bAtoCHAxWU_rsIHdewCL8QFnoECBcQAQ&usg=AOvVaw0Vp6argRF1d_gV34Q1D5t9), a network scanner app.
- Enter it into a web browser to access the Tasmota web interface.

## Reassembly
- At this point, you have two options:
  1. Reassemble and connect to mains power:
     - Unplug the USB adapter and disconnect all wires.
     - Carefully reassemble the switch.
     - Connect the reassembled switch to mains power.
     - This approach allows for a more realistic setup during configuration.
  2. Continue with an "open" configuration:
     - Keep the logic board exposed.
     - Proceed with the configuration process while the device remains disassembled.
     - Choose this if you want to make further modifications to the firmware, i.e. go back to the stock firmware.
- For this guide, we'll proceed with the reassembled switch connected to mains power.

## Basic Switch Configuration
- From the web interface, select *Configure* → *Configure Other*. This brings you to this screen:

<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/7d73b4b9-e472-4dda-a6bf-91805a30525f" width="25%" height="25%" alt="TXU02 - Configure Other" onclick="window.open(this.src, '_blank');">

- The line of text below "Template" will look different when installing Tasmota for the first time. Here we need to enter the proper template string.
  - Get it from this website: [templates.blakadder.com](https://templates.blakadder.com/sonoff_T5-3C-86.html)
  - Be sure to select the proper model (1/2/3/4-Gang) as well as US or EU versions. Just use the search function in the menu and enter `Sonoff TX Ultimate T5` to see all models.
  - Copy the string provided in the Details page below the text *Configuration for ESP32*
  - For my switch model (sonoff-tx-ultimate-t5-3c-86) it is: `{"NAME":"TX Ultimate 3","GPIO":[0,0,7808,0,7840,3872,0,0,0,1376,0,7776,0,225,224,3232,0,480,3200,0,0,0,3840,226,0,0,0,0,0,0,0,0,0,0,0,0],"FLAG":0,"BASE":1,"CMND":"Backlog Pixels 28"}`
- Enter the string in the edit field where it says *Template*
- Choose names that help you identify all parts of the switch later: *Device Name* for the switch and the *Friendly Names* for the relays. In my case, 1-3 are the actual relays and friendly name 4 is a virtual switch for the LEDs around the switch.
- Click the *Save* button and the switch will reboot. This will provide the following web interface where you can play around a bit with the color sliders for the LEDs and hear the relays when you click the *Toggle* buttons.
- Note: A physical interaction with the switch (i.e., touching it) will not do anything yet!

<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/97b0c81b-d96f-47d0-9cf0-d8613b3b52e0" width="25%" height="25%" alt="TXU02 - Main Menu" onclick="window.open(this.src, '_blank');">

## Advanced configuration
- To get touch, haptics and sound to behave like in the stock firmware we have a lot to do. First, let's enable touch, thanks to [blakadder.com](https://templates.blakadder.com/sonoff_T5-1C-86) for the original guide and the files.
- Download the touch driver file [txultimate.be](https://raw.githubusercontent.com/blakadder/tx-ultimate/main/txultimate.be)
- In the Tasmota web interface navigate to *Tools* → *Manage File system*
- Upload the file to the switch by selecting *Choose file* then select the previous downloaded txultimate.be, finally click *Start Upload*. You will then see the file in the list below the *Upload* button.
- The touch driver needs to be loaded on startup.
  - So select *Create and edit new file*.
  - In the *File* edit box enter the text `/autoexec.be`.
  - In the edit area below delete the prefilled text (newfile.txt) enter `load("txultimate.be")` instead. It should look like this:

<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/824bd593-6e3d-41b3-87ff-0b79a57bd255" width="25%" height="25%" alt="TXU02 - Edit File" onclick="window.open(this.src, '_blank');">
  
  - Select *Save*. This is how it should all look like:
   
<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/5ac0b4af-6afb-4e02-b3ba-bb73c421ea24" width="25%" height="25%" alt="TXU02 - Manage File system" onclick="window.open(this.src, '_blank');">
  
- Restart the switch from the main menu. This will load the touch driver, enabling it to recognize touch events.
- Caution: Ensure you do not touch any high-power components such as the exposed screws form the screw-terminals. Especually if the device is in a test setup on your desk, consider using insulation tape to cover any exposed conductive parts.

<img src="https://github.com/markus-barta/sonoff-tx-ultimate-t5-3c-86/assets/276789/3729d304-0405-43b0-b605-8c43f47faaba" width="25%" height="25%" alt="powered-switch" onclick="window.open(this.src, '_blank');">

- To verify the touch driver works, navigate to *Tools* → *Console* and touch the switch. Observe the RESULT topic in the log for confirmation. You should find log-lines like this:
```
11:21:19.216 Touch event: 0 pos: 1
11:21:19.314 bytes('AA5501020101BF04')
11:21:19.316 Short press zone: 1
11:21:25.364 bytes('AA55010202000568F0')
11:21:25.366 Touch event: 0 pos: 5
11:21:25.414 bytes('AA5501020105FF80')
11:21:25.416 Short press zone: 5
11:21:29.314 bytes('AA55010202000A991F')
11:21:29.316 Touch event: 0 pos: 10
11:21:29.414 bytes('AA550102010A0E6F')
11:21:29.416 Short press zone: 10
```
- There are events firing based on where, how and how long you touch the switch. e.g. pos 1 is the left switch, pos 5 the middle one and pos 10 the right one.
- Again, note that there are no "default" actions set in the firmware/config (yet). So touching only results in events in the log, no sound, no light, no haptics, no relay that changes.
