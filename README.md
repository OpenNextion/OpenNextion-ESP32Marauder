# OpenNextion ESP32 Marauder

[![English](https://img.shields.io/badge/lang-English-blue)](./README.md)
[![中文](https://img.shields.io/badge/lang-中文-red)](./README.zh-CN.md)

<p align="center">
  <img src="docs/images/opennextion-esp32-marauder-demo-a8f4c2.jpg" alt="OpenNextion ESP32 Marauder demo on OpenNextion display" width="820">
</p>

OpenNextion ESP32 Marauder is an OpenNextion board support fork of
[ESP32 Marauder](https://github.com/justcallmekoko/ESP32Marauder). It adds
ready-to-build firmware targets for OpenNextion ESP32-S3 rectangular display
boards with SPI TFT LCD, CST826 capacitive touch, OPI PSRAM, and SDMMC storage.

This repository is intended to make ESP32 Marauder easier to build, flash, and
validate on supported OpenNextion development boards while the upstream board
support pull requests are under review.

## Supported Displays

The current public release targets two OpenNextion portrait displays:

| Display model | Size | Resolution | Orientation | Status |
| --- | --- | --- | --- | --- |
| [ONX3248G035][onx3248g035] | 3.5 inch | 320 x 480 | Portrait | Verified |
| [ONX2432G028][onx2432g028] | 2.8 inch | 240 x 320 | Portrait | Verified |

Build-time board selection is explicit:

```sh
arduino-cli compile --build-property "compiler.cpp.extra_flags=-DMARAUDER_ONX3248G035" esp32_marauder
arduino-cli compile --build-property "compiler.cpp.extra_flags=-DMARAUDER_ONX2432G028" esp32_marauder
```

Do not flash firmware built for one display model onto the other display model.

## Quick Start

1. Check your board model in [Supported Displays](#supported-displays).
2. Download the matching `.bin` file from the latest GitHub Release.
3. Flash the full image at address `0x0`.
4. Use the touchscreen menu zones to navigate the ESP32 Marauder UI.
5. For source builds, see [Build and flash from source](docs/BUILD_AND_FLASH.md).

## Firmware Download and Flashing

Download firmware from the latest GitHub Release page. The current release
provides one full initial flashing image per supported display model. A merged binary is
intended for full initial flashing from address `0x0`.

| Display model | Firmware file | Flash address | Version |
| --- | --- | --- | --- |
| [ONX3248G035][onx3248g035] | `opennextion-esp32-marauder-v0.1.0-onx3248g035.bin` | `0x0` | `v0.1.0` |
| [ONX2432G028][onx2432g028] | `opennextion-esp32-marauder-v0.1.0-onx2432g028.bin` | `0x0` | `v0.1.0` |

Flash a merged binary with:

```sh
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 ./opennextion-esp32-marauder-v0.1.0-onx2432g028.bin
```

Replace the serial port and firmware file name as needed for your board.

For this project, full firmware flashing is recommended. OTA firmware downloads
are not provided unless the OTA flow is separately validated. For older releases,
use the matching firmware files and SHA256 values from each GitHub Release page.

## Touchscreen Navigation

The supported OpenNextion boards do not use discrete hardware buttons for the
ESP32 Marauder UI. Navigation and scan controls use the existing ESP32 Marauder
touchscreen menu and virtual button behavior, with layout adjustments for the
OpenNextion screen sizes.

On menu screens, the display is divided into three touch zones:

- Top area: move selection up
- Middle area: select / enter
- Bottom area: move selection down

During scan or monitor screens, the firmware uses the existing ESP32 Marauder
on-screen virtual buttons such as `X`, `-`, `+`, and `HOP` when available.

## Background

ESP32 Marauder is an excellent ESP32 WiFi and Bluetooth security tool project.
The upstream project already supports multiple ESP32-based devices, but the
OpenNextion ESP32-S3 display boards need dedicated board-level configuration for
LCD initialization, touch input, PSRAM, SDMMC storage, and build automation.

This fork keeps the original ESP32 Marauder application behavior and adds the
OpenNextion-specific hardware support needed by the supported boards.

## 3D Printed Enclosure

I also designed simple 3D printed enclosures for the supported OpenNextion
display sizes and published them on MakerWorld. Anyone who needs them can
download and print them for free.

Each enclosure is a single-piece print and is easy to install. Peel off the tape
around the edge of the matching OpenNextion display, then press the display into
the printed enclosure and use the adhesive edge to hold it in place.

MakerWorld project links:

- 3.5 inch ONX3248G035 enclosure: link to be added
- 2.8 inch ONX2432G028 enclosure: link to be added

## Current Porting Work

This version is based on ESP32 Marauder and adds OpenNextion multi-board
support. The main changes are:

### 1. OpenNextion Board Support

OpenNextion ESP32 Marauder includes dedicated board support for:

- [ONX3248G035][onx3248g035] 3.5 inch portrait display
- [ONX2432G028][onx2432g028] 2.8 inch portrait display

Each board has its own TFT_eSPI setup file and board macro. The selected board
is controlled at build time by `MARAUDER_ONX3248G035` or
`MARAUDER_ONX2432G028`.

### 2. Display and Touch Initialization

The port adds the OpenNextion display and touch initialization required by the
supported boards:

- ST7796U TFT setup for ONX3248G035
- ST7789 TFT setup for ONX2432G028
- CST826 I2C capacitive touch support
- PCF8574 IO expander support for LCD reset and SDCS control
- Board-specific TFT_eSPI setup selection during local and CI builds

### 3. PSRAM and SDMMC Support

Both supported boards use ESP32-S3R8 modules with 16 MB flash and 8 MB OPI
PSRAM. The port also enables 1-bit SDMMC support for the onboard SD card slot,
so ESP32 Marauder file features can use the onboard storage.

### 4. GitHub Actions Build Targets

The project includes GitHub Actions board matrix entries for both OpenNextion
targets. The build targets use Arduino-ESP32 `2.0.11`, NimBLE-Arduino `1.3.8`,
`PartitionScheme=default_8MB`, `FlashSize=16M`, `PSRAM=opi`, and UART0
upload/serial settings.

## Current Validation Status

### Display Validation

<p align="center">
  <img src="docs/images/opennextion-esp32-marauder-validation-6d91b7.jpg" alt="OpenNextion ESP32 Marauder UI on OpenNextion display" width="720">
</p>

- [ONX3248G035][onx3248g035] portrait mode has been validated on real hardware
- [ONX2432G028][onx2432g028] portrait mode has been validated on real hardware
- Clean source builds passed for both OpenNextion board targets
- Display color order has been validated on hardware
- CST826 touch input has been validated with the ESP32 Marauder touch UI
- SD card mounting and directory listing have been validated through SDMMC

### Firmware Validation Matrix

Legend: ✅ Verified / ⚠️ Partially verified or hardware-dependent / ⏳ Not tested

| Board | Build | Boot | Display | Touch | SDMMC | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| ONX3248G035 | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | 3.5 inch ST7796U display |
| ONX2432G028 | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | 2.8 inch ST7789 display |

## Local Build, Flash and Monitor

For source builds, separate build output flashing, serial monitoring, and merged
binary packaging, see [Build and flash from source](docs/BUILD_AND_FLASH.md).

## Documentation

- [Build and flash from source](docs/BUILD_AND_FLASH.md)
- [Flash release firmware](docs/RELEASE_FLASHING.md)
- [Supported boards](docs/SUPPORTED_BOARDS.md)
- [Publication policy](docs/PUBLICATION_POLICY.md)

## Roadmap

Planned next steps:

- Keep the OpenNextion fork aligned with upstream ESP32 Marauder where practical
- Publish convenient merged firmware binaries in GitHub Releases
- Add enclosure links when the 3D printed enclosure pages are ready
- Continue validating display, touch, SD, and UI behavior on supported hardware

## Credits

This project is based on ESP32 Marauder. Thanks to the original author and the
related open source projects.

- ESP32 Marauder: https://github.com/justcallmekoko/ESP32Marauder
- OpenNextion open source projects: https://github.com/OpenNextion

## License

This project preserves the upstream ESP32 Marauder license terms.

ESP32 Marauder is licensed under the MIT License. See [LICENSE](LICENSE) for details.
Third-party libraries may have their own license notices.

## Disclaimer

This project is not the official upstream ESP32 Marauder project.

ESP32 Marauder is a WiFi and Bluetooth security tool. Use it only on networks,
devices, and radio environments where you have permission to test. Flashing and
using third-party firmware involves risk. Please use it only after understanding
the risks. This project is not responsible for device damage, data loss, network
connection issues, legal consequences, or any other consequences of use.

[onx3248g035]: https://nextion.tech/wiki/onx3248g035/
[onx2432g028]: https://nextion.tech/wiki/onx2432g028/
