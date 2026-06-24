<!---[![License: MIT](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/justcallmekoko/ESP32Marauder/blob/master/LICENSE)--->
<!---[![Gitter](https://badges.gitter.im/justcallmekoko/ESP32Marauder.png)](https://gitter.im/justcallmekoko/ESP32Marauder)--->
<!---[![Build Status](https://travis-ci.com/justcallmekoko/ESP32Marauder.svg?branch=master)](https://travis-ci.com/justcallmekoko/ESP32Marauder)--->
<!---Shields/Badges https://shields.io/--->

# ESP32 Marauder
<p align="center"><img alt="Marauder logo" src="https://github.com/justcallmekoko/ESP32Marauder/blob/master/pictures/marauder_skull_patch_04_full_final.png?raw=true" width="300"></p>
<p align="center">
  <b>A suite of WiFi/Bluetooth offensive and defensive tools for the ESP32</b>
  <br><br>
  <a href="https://github.com/justcallmekoko/ESP32Marauder/blob/master/LICENSE"><img alt="License" src="https://img.shields.io/github/license/mashape/apistatus.svg"></a>
  <a href="https://gitter.im/justcallmekoko/ESP32Marauder"><img alt="Gitter" src="https://badges.gitter.im/justcallmekoko/ESP32Marauder.png"/></a>
  <br>
  <a href="https://twitter.com/intent/follow?screen_name=jcmkyoutube"><img src="https://img.shields.io/twitter/follow/jcmkyoutube?style=social&logo=twitter" alt="Twitter"></a>
  <a href="https://www.instagram.com/just.call.me.koko"><img src="https://img.shields.io/badge/Follow%20Me-Instagram-orange" alt="Instagram"/></a>
  <br><br>
</p>
    
[![Build and Push](https://github.com/justcallmekoko/ESP32Marauder/actions/workflows/build_push.yml/badge.svg)](https://github.com/justcallmekoko/ESP32Marauder/actions/workflows/build_push.yml)

## OpenNextion Board Support

This fork adds ESP32 Marauder support for the following OpenNextion ESP32-S3 boards while the upstream pull requests are under review.

| Board | Display | Touch | Storage | Build flag | TFT setup |
| --- | --- | --- | --- | --- | --- |
| [ONX2432G028](https://github.com/OpenNextion/OpenNextion-SKU-ONX2432G028) | 2.8 inch ST7789, 240 x 320 | CST826 I2C capacitive touch | 1-bit SDMMC | `MARAUDER_ONX2432G028` | `User_Setup_onx2432g028.h` |
| [ONX3248G035](https://github.com/OpenNextion/OpenNextion-SKU-ONX3248G035) | 3.5 inch ST7796U, 320 x 480 | CST826 I2C capacitive touch | 1-bit SDMMC | `MARAUDER_ONX3248G035` | `User_Setup_onx3248g035.h` |

Both targets use ESP32-S3R8 modules with 16 MB flash and 8 MB OPI PSRAM. The board-level GitHub Actions targets use Arduino-ESP32 `2.0.11`, NimBLE-Arduino `1.3.8`, `PartitionScheme=default_8MB`, `FlashSize=16M`, `PSRAM=opi`, and UART0 upload/serial settings.

### Build ONX2432G028

```bash
arduino-cli compile \
  --fqbn "esp32:esp32:esp32s3:PartitionScheme=default_8MB,FlashSize=16M,PSRAM=opi,CDCOnBoot=default,UploadMode=default" \
  --warnings none \
  --build-path /private/tmp/onx2432-build \
  --build-property "compiler.cpp.extra_flags=-DMARAUDER_ONX2432G028" \
  esp32_marauder
```

### Build ONX3248G035

```bash
arduino-cli compile \
  --fqbn "esp32:esp32:esp32s3:PartitionScheme=default_8MB,FlashSize=16M,PSRAM=opi,CDCOnBoot=default,UploadMode=default" \
  --warnings none \
  --build-path /private/tmp/onx3248-build \
  --build-property "compiler.cpp.extra_flags=-DMARAUDER_ONX3248G035" \
  esp32_marauder
```

Before local builds, make sure the matching TFT_eSPI setup file is selected in `User_Setup_Select.h`. The GitHub Actions workflow performs this selection automatically through the board matrix.

### Flash Firmware

Use the board-specific build directory from the compile command.

```bash
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 /private/tmp/onx2432-build/esp32_marauder.ino.bootloader.bin \
  0x8000 /private/tmp/onx2432-build/esp32_marauder.ino.partitions.bin \
  0xe000 ~/Library/Arduino15/packages/esp32/hardware/esp32/2.0.11/tools/partitions/boot_app0.bin \
  0x10000 /private/tmp/onx2432-build/esp32_marauder.ino.bin
```

For ONX3248G035, replace `/private/tmp/onx2432-build` with `/private/tmp/onx3248-build`.

### Monitor Serial Log

```bash
python -m serial.tools.miniterm /dev/cu.wchusbserial1110 115200
```

The firmware should boot to the ESP32 Marauder serial prompt after display, touch, settings, and SD initialization.

### Generate a Single Merged Binary

```bash
python -m esptool --chip esp32s3 merge_bin \
  -o ./onx2432g028_merged.bin \
  --flash_mode dio \
  --flash_freq 80m \
  --flash_size 16MB \
  0x0 /private/tmp/onx2432-build/esp32_marauder.ino.bootloader.bin \
  0x8000 /private/tmp/onx2432-build/esp32_marauder.ino.partitions.bin \
  0xe000 ~/Library/Arduino15/packages/esp32/hardware/esp32/2.0.11/tools/partitions/boot_app0.bin \
  0x10000 /private/tmp/onx2432-build/esp32_marauder.ino.bin
```

Flash the merged binary from address `0x0`:

```bash
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 ./onx2432g028_merged.bin
```

For ONX3248G035, use `/private/tmp/onx3248-build` and output `./onx3248g035_merged.bin`.

## Getting Started
Download the [latest release](https://github.com/justcallmekoko/ESP32Marauder/releases/latest) of the firmware.  

Check out the project [wiki](https://github.com/justcallmekoko/ESP32Marauder/wiki) for a full overview of the ESP32 Marauder

# For Sale Now
You can buy the ESP32 Marauder using [this link](https://www.justcallmekokollc.com)
