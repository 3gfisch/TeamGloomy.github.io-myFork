---
title: Connecting an SKR v1.1 via Wifi
tags: []
keywords: 
last_updated: 21/01/2021
summary: "Connecting an SKR v1.1 via Wifi"
sidebar: mydoc_sidebar
permalink: skr_1.1_connected_wifi.html
folder: mydoc
comments: false
toc: false
datatable: true
---

## Overview

The SKR v1.1 is an LPC1768 based board.

## Firmware File

Choose the correct corresponding firmware (firmware-lpc-esp8266wifi.bin) from [here](https://github.com/gloomyandy/RepRapFirmware/releases). Remember to rename it to firmware.bin. Put it in the root of a FAT32 formatted SD card.   

## Wifi

Use a nodemcu ESP8266 with USB programming as it already 5v tolerant and it allows for updating via USB.

### BOM

* 1 x nodemcu ESP8266 or Wemos D1 mini
* 3 x 47R resistor
* 1 x 470R resistor
* 3 x 2200R resistor
* jumpers or other ways of connecting to the SKR

### Preparing the ESP

Follow the instructions [here](mydoc_lpc_esp.html).

### Connecting the ESP

The pinout for the SKR v1.1 can be found [here](https://github.com/gloomyandy/RepRapFirmware/wiki/SKR-1.1-Pins) and the schematic for the Duet 2 Wifi for reference can be found [here](https://github.com/T3P3/Duet/blob/master/Duet2/Duet2v1.04/DuetWifiv1.04a_Schematic.pdf). 

The table below shows the pins required on the ESP and what they are connected to on the SKR. Please ensure that your cables are no longer than 30cm although they should ideally be as short as possible.  

<div class="datatable-begin"></div>

| ESP Pin       | SKR Pin       | Resistor Value  |
| :-------------: |:-------------:| :---------------:|
| RST           | 1.31 on EXP2         | 470R            |
| CS/GPIO15     | 0.16 on EXP1         | 2200R           |
| MOSI/GPIO13   | 0.18 on EXP1         | 47R             |
| MISO/GPIO12   | 0.17 on EXP2         | 47R             |
| SCLK/GPIO14  | 0.15 on EXP1         | 47R             |
| ESP_DATA_Ready/GPIO0   | 2.11 on EXP1         | 2200R             |
| LPC_DATA_Ready/GPIO4   | 1.30 on EXP1         | None            |
| VIN(5v)   | 5v on EXP1          | None             |
| GND   | GND on EXP1          | 2200R to RST             |

<div class="datatable-end"></div>

### Prepare the SD Card

Follow the instructions on [Getting Started with RRF3](https://github.com/gloomyandy/RepRapFirmware/wiki/Getting-Started---RRF3)

### Board.txt file

You will also need a board.txt file in the sys folder. Below are the contents that should be used. 

```
//Config for BIQU SKR v1.1
lpc.board = biquskr_1.1
//wifi pins
8266wifi.espDataReadyPin = 2.11
8266wifi.lpcTfrReadyPin = 1.30
8266wifi.espResetPin = 1.31
```

### Final Setup

Once connected, power up the board using 12-24v and connect to the USB port on the board. Using a program such as [termite](https://www.compuphase.com/software_termite.htm), connect to the board. As of release 3.2_4, the recommended terminal program is [YAT](https://sourceforge.net/projects/y-a-terminal/). Then type in the following

```
M552 S0
M587 S"your SSID" P"your password"
M552 S1
```

{% include warning.html content="**DO NOT USE PRONTERFACE** it will convert all text to upper case. If you really must, please do the following. <br/>  If you wanted to use “PassWord”, you would write P”P’a’s’sW’o’r’d” with the ‘ indicating the following letter should be lower case. Explanation [here](https://duet3d.dozuki.com/Wiki/Gcode#Section_M587_Add_WiFi_host_network_to_remembered_list_or_list_remembered_networks)." %}

The blue light on the wifi chip shoould then flash blue and will go solid when a connection has been established. The ip address will be shown on the serial connection. It is also possible to type just M552 to get the current ip address reported back.

The final thing to do is add the line “M552 S1” to your config file. This can be done through the web interface. This just ensures that the wifi connection is started at start up. There is no need to add the M587 command as this is written permanently to the flash of the ESP chip.