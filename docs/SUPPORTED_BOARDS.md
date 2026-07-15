# Supported Boards

This document lists the public board support status for OpenNextion ESP32
Marauder.

## Current Included Targets

| Board | Display size | Resolution | Orientation | Release status | Build flag |
| --- | --- | --- | --- | --- | --- |
| [ONX3248G035](https://nextion.tech/wiki/onx3248g035/) | 3.5 inch | 320 x 480 | Portrait | Included | `MARAUDER_ONX3248G035` |
| [ONX2432G028](https://nextion.tech/wiki/onx2432g028/) | 2.8 inch | 240 x 320 | Portrait | Included | `MARAUDER_ONX2432G028` |

Both included targets use:

```text
Board FQBN: esp32:esp32:esp32s3
PartitionScheme=default_8MB
FlashSize=16M
PSRAM=opi
CDCOnBoot=default
UploadMode=default
```

The current release provides one full initial flashing image per supported display model. Do not flash firmware built for one board onto the other board.

## Not Included

| Board / target | Status | Notes |
| --- | --- | --- |
| ONX3248G035 landscape | Not a public target | Requires separate UI and touch validation |
| ONX2432G028 landscape | Not a public target | Requires separate UI and touch validation |
| Other OpenNextion / Nextion screens | Not supported unless explicitly listed | Each board needs its own display, touch, backlight, resolution, orientation, and UI validation |

## Notes

- Do not assume that another OpenNextion or Nextion display is compatible only
  because it uses ESP32.
- Each board needs its own display, touch, backlight, resolution, orientation,
  storage, and UI validation.
- Firmware for unsupported boards or orientations should not be flashed unless a
  matching public board profile and release asset are provided.
- Release firmware is subject to the repository `LICENSE` and third-party
  dependency licenses.
