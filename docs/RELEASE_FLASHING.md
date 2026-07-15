# Flashing Release Firmware

This document is for users flashing a firmware image downloaded from a GitHub
Release.

The current release provides full initial flashing images for:

- [ONX3248G035](https://nextion.tech/wiki/onx3248g035/) 3.5 inch portrait
- [ONX2432G028](https://nextion.tech/wiki/onx2432g028/) 2.8 inch portrait

OTA firmware is not provided unless separately validated.

## Release Assets

Download the file that matches your display model from the latest GitHub Release page:

| Display model | Firmware file |
| --- | --- |
| ONX3248G035 | `opennextion-esp32-marauder-v0.1.0-onx3248g035.bin` |
| ONX2432G028 | `opennextion-esp32-marauder-v0.1.0-onx2432g028.bin` |

Do not flash an ONX3248G035 image onto ONX2432G028, or an ONX2432G028 image
onto ONX3248G035.

Firmware files are release assets only. They are not committed to git.

## Verify The Download

Before flashing, verify the SHA256 of the downloaded file. The expected SHA256
values should be listed in the GitHub Release notes.

macOS / Linux:

```sh
shasum -a 256 <FIRMWARE_FILE>.bin
```

Windows PowerShell:

```powershell
Get-FileHash .\<FIRMWARE_FILE>.bin -Algorithm SHA256
```

The result must match the SHA256 shown in the release notes for that exact file.

## Flash The Full Initial Image

The release assets are full initial flashing images. Write the matching image at
address `0x0`.

```sh
python -m esptool --chip esp32s3 \
  -p <SERIAL_PORT> \
  -b 921600 \
  write_flash 0x0 <FIRMWARE_FILE>.bin
```

Replace `<SERIAL_PORT>` with the serial device for your computer, and replace
`<FIRMWARE_FILE>.bin` with the release asset for your display model.

## OTA Status

OTA firmware is not provided unless separately validated.

Use the full initial flashing image for the current release.

## License And Risk Notice

This project preserves the upstream ESP32 Marauder MIT License terms.

ESP32 Marauder is a WiFi and Bluetooth security tool. Use it only on networks,
devices, and radio environments where you have permission to test. Flashing
third-party firmware involves risk. Use this firmware only after understanding
the risks and only on hardware you are prepared to recover or reflash yourself.
