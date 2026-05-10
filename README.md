# RLCD42 (ESP32-S3 + LVGL)

Firmware project built with ESP-IDF for an ESP32-S3 board with an RLCD display, LVGL UI, audio codec features, Wi-Fi/BLE scanning, and SD card support.

## Overview

This repository uses a component-based structure:

- `main`: app entry and startup wiring.
- `components/port_bsp`: low-level board/peripheral drivers (display, I2C, SD, buttons, codec glue).
- `components/app_bsp`: LVGL port and connectivity services (Wi-Fi/BLE).
- `components/ui_bsp`: UI source/generated files.
- `components/user_app`: app logic and FreeRTOS tasks.
- `components/ExternLib`: local external libraries.

## Requirements

- ESP-IDF installed and exported in your shell (project is configured for ESP32-S3).
- Python environment required by ESP-IDF tools.
- USB serial access to the target board.

Key defaults in this project:

- Target: `esp32s3`
- Flash size/mode: `16MB`, `QIO`
- SPIRAM enabled
- Custom partition table in `partitions.csv`

## Quick Start

From project root:

```bash
idf.py set-target esp32s3
idf.py build
```

Flash and monitor:

```bash
idf.py -p /dev/tty.usbmodem1101 flash
idf.py -p /dev/tty.usbmodem1101 monitor
```

If your serial port differs, replace `/dev/tty.usbmodem1101`.

## Common Commands

```bash
# Reconfigure project options
idf.py menuconfig

# Clean build output
idf.py fullclean

# Build only
idf.py build
```

## Dependencies

Managed via `main/idf_component.yml`:

- `lvgl/lvgl`
- `espressif/avi_player`
- `espressif/esp_new_jpeg`

## Notes

- Keep LVGL object updates synchronized with the LVGL lock flow from `components/app_bsp/lvgl_bsp.cpp`.
- Avoid committing generated build outputs under `build/`.
- Large buffers (for example audio) rely on SPIRAM; be mindful of memory capabilities when changing allocation paths.
