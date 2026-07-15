# Build And Flash From Source

This document is for developers who want to build OpenNextion ESP32 Marauder
from source.

For normal users, prefer the GitHub Release full initial flashing images and see
`RELEASE_FLASHING.md`.

## Supported Source Build Targets

The current source tree supports two OpenNextion portrait source build targets:

| Board | Display | Orientation | Build flag |
| --- | --- | --- | --- |
| ONX3248G035 | 3.5 inch, 320 x 480 | portrait | `MARAUDER_ONX3248G035` |
| ONX2432G028 | 2.8 inch, 240 x 320 | portrait | `MARAUDER_ONX2432G028` |

Passing the board flag explicitly is required because the same source tree builds
multiple board targets.

## Environment

Use Arduino-ESP32 `2.0.11` for builds that match the public OpenNextion targets.
The GitHub Actions board matrix also uses NimBLE-Arduino `1.3.8`.

Confirm your local toolchain before building:

```sh
arduino-cli version
arduino-cli core list
python -m esptool version
```

The board FQBN used by the validated OpenNextion builds is:

```text
esp32:esp32:esp32s3:PartitionScheme=default_8MB,FlashSize=16M,PSRAM=opi,CDCOnBoot=default,UploadMode=default
```

## Build ONX3248G035

From the repository root:

```sh
rm -rf /private/tmp/onx3248-build /private/tmp/onx3248-libs
mkdir -p /private/tmp/onx3248-libs

cp -R ~/Documents/Arduino/libraries/TFT_eSPI /private/tmp/onx3248-libs/CustomTFT_eSPI
rm -f /private/tmp/onx3248-libs/CustomTFT_eSPI/User_Setup_Select.h
cp User*.h /private/tmp/onx3248-libs/CustomTFT_eSPI/

sed -i '' 's|^//#include <User_Setup_onx3248g035.h>|#include <User_Setup_onx3248g035.h>|' \
  /private/tmp/onx3248-libs/CustomTFT_eSPI/User_Setup_Select.h

arduino-cli compile \
  --fqbn "esp32:esp32:esp32s3:PartitionScheme=default_8MB,FlashSize=16M,PSRAM=opi,CDCOnBoot=default,UploadMode=default" \
  --library /private/tmp/onx3248-libs/CustomTFT_eSPI \
  --libraries libraries \
  --warnings none \
  --build-path /private/tmp/onx3248-build \
  --build-property "compiler.cpp.extra_flags=-DMARAUDER_ONX3248G035" \
  --build-property "compiler.c.elf.extra_flags=-Wl,--allow-multiple-definition" \
  esp32_marauder
```

## Build ONX2432G028

From the repository root:

```sh
rm -rf /private/tmp/onx2432-build /private/tmp/onx2432-libs
mkdir -p /private/tmp/onx2432-libs

cp -R ~/Documents/Arduino/libraries/TFT_eSPI /private/tmp/onx2432-libs/CustomTFT_eSPI
rm -f /private/tmp/onx2432-libs/CustomTFT_eSPI/User_Setup_Select.h
cp User*.h /private/tmp/onx2432-libs/CustomTFT_eSPI/

sed -i '' 's|^//#include <User_Setup_onx2432g028.h>|#include <User_Setup_onx2432g028.h>|' \
  /private/tmp/onx2432-libs/CustomTFT_eSPI/User_Setup_Select.h

arduino-cli compile \
  --fqbn "esp32:esp32:esp32s3:PartitionScheme=default_8MB,FlashSize=16M,PSRAM=opi,CDCOnBoot=default,UploadMode=default" \
  --library /private/tmp/onx2432-libs/CustomTFT_eSPI \
  --libraries libraries \
  --warnings none \
  --build-path /private/tmp/onx2432-build \
  --build-property "compiler.cpp.extra_flags=-DMARAUDER_ONX2432G028" \
  --build-property "compiler.c.elf.extra_flags=-Wl,--allow-multiple-definition" \
  esp32_marauder
```

## Package Full Initial Flashing Images

To package a full initial flashing image for ONX3248G035:

```sh
python -m esptool --chip esp32s3 merge_bin \
  -o ./opennextion-esp32-marauder-v0.1.0-onx3248g035.bin \
  --flash_mode dio \
  --flash_freq 80m \
  --flash_size 16MB \
  0x0 /private/tmp/onx3248-build/esp32_marauder.ino.bootloader.bin \
  0x8000 /private/tmp/onx3248-build/esp32_marauder.ino.partitions.bin \
  0xe000 ~/Library/Arduino15/packages/esp32/hardware/esp32/2.0.11/tools/partitions/boot_app0.bin \
  0x10000 /private/tmp/onx3248-build/esp32_marauder.ino.bin
```

To package a full initial flashing image for ONX2432G028:

```sh
python -m esptool --chip esp32s3 merge_bin \
  -o ./opennextion-esp32-marauder-v0.1.0-onx2432g028.bin \
  --flash_mode dio \
  --flash_freq 80m \
  --flash_size 16MB \
  0x0 /private/tmp/onx2432-build/esp32_marauder.ino.bootloader.bin \
  0x8000 /private/tmp/onx2432-build/esp32_marauder.ino.partitions.bin \
  0xe000 ~/Library/Arduino15/packages/esp32/hardware/esp32/2.0.11/tools/partitions/boot_app0.bin \
  0x10000 /private/tmp/onx2432-build/esp32_marauder.ino.bin
```

The merged image is a full image for initial flashing at address `0x0`. Do not
commit generated firmware files to git.

## Flash Separate Build Outputs

Use the matching build directory. Do not invent alternate partition layouts or
write offsets.

```sh
python -m esptool --chip esp32s3 -p <SERIAL_PORT> -b 921600 write_flash \
  0x0 /private/tmp/onx2432-build/esp32_marauder.ino.bootloader.bin \
  0x8000 /private/tmp/onx2432-build/esp32_marauder.ino.partitions.bin \
  0xe000 ~/Library/Arduino15/packages/esp32/hardware/esp32/2.0.11/tools/partitions/boot_app0.bin \
  0x10000 /private/tmp/onx2432-build/esp32_marauder.ino.bin
```

For ONX3248G035, use `/private/tmp/onx3248-build`. Replace `<SERIAL_PORT>` with
the serial device for your computer.

## Serial Monitor

```sh
python -m serial.tools.miniterm <SERIAL_PORT> 115200
```

The firmware should boot to the ESP32 Marauder serial prompt after display,
touch, settings, and SD initialization.

## Serial / Flashing Rules

- Use the build output from the matching board target.
- Use full merged images at address `0x0` for release-style flashing.
- Do not flash ONX3248G035 firmware onto ONX2432G028, or the reverse.
- Do not change partition tables, flash offsets, reset flags, or baud rate as a
  workaround for a failed flash.
- If flashing fails, record the exact command and error output, then fix the
  serial/toolchain issue before retrying.

## Release Build Consistency

For public release builds, the release version must match the intended GitHub
Release tag and firmware asset name. Replace `v0.1.0` with the version being
published when preparing a later release. For example:

```text
Release tag:  v0.1.0
ONX3248G035:  opennextion-esp32-marauder-v0.1.0-onx3248g035.bin
ONX2432G028:  opennextion-esp32-marauder-v0.1.0-onx2432g028.bin
Build flags:  MARAUDER_ONX3248G035 / MARAUDER_ONX2432G028
```

All release notes, checksums, and README references should agree before firmware
images are published.

## License

This project preserves the upstream ESP32 Marauder MIT License terms. Third-party
libraries may have their own license notices.
