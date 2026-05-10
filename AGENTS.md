# AGENTS

This repository is an ESP-IDF application for ESP32-S3 with LVGL UI, board support layers, and user-level app tasks.

## Start Here

- Target and defaults: [sdkconfig.defaults](sdkconfig.defaults)
- Top-level build entry: [CMakeLists.txt](CMakeLists.txt)
- App entrypoint: [main/main.cpp](main/main.cpp)
- Main app logic/tasks: [components/user_app/user_app.cpp](components/user_app/user_app.cpp)

## Build, Flash, Monitor

- Build: run ESP-IDF build for this workspace.
- Flash: use UART flash (workspace default in [.vscode/settings.json](.vscode/settings.json)).
- Monitor: run serial monitor after flashing.
- Clean when config/component graph changes: full clean, then build.

If using CLI instead of the ESP-IDF extension, standard commands are:

- idf.py build
- idf.py -p <PORT> flash
- idf.py -p <PORT> monitor
- idf.py fullclean

## Architecture And Ownership

- [main](main): app bootstrap and LVGL display flush callback wiring.
- [components/port_bsp](components/port_bsp): low-level board drivers (display, I2C, SD, buttons, codec glue).
- [components/app_bsp](components/app_bsp): platform services (LVGL port, Wi-Fi, BLE scan).
- [components/ui_bsp](components/ui_bsp): UI source and generated assets.
- [components/user_app](components/user_app): app behavior, FreeRTOS tasks, integration logic.
- [components/ExternLib](components/ExternLib): local reusable libraries.

When changing behavior, prefer editing the highest-level component that owns it instead of patching lower layers.

## Project Conventions

- Keep UI calls thread-safe: LVGL object updates should run under the LVGL lock model from [components/app_bsp/lvgl_bsp.cpp](components/app_bsp/lvgl_bsp.cpp).
- Keep hardware pin and display assumptions consistent with [main/main.cpp](main/main.cpp) and [components/port_bsp/display_bsp.cpp](components/port_bsp/display_bsp.cpp).
- Do not edit generated build output under [build](build).
- Preserve component registration style in each component CMake file.

## High-Risk Areas

- Memory-sensitive paths: audio/frame buffers use SPIRAM; check heap capability usage in [components/user_app/user_app.cpp](components/user_app/user_app.cpp).
- Display timing/performance: flush callback and pixel loops in [main/main.cpp](main/main.cpp) can affect frame rate.
- Partition/flash layout changes: validate [partitions.csv](partitions.csv) and related sdkconfig settings together.

## External Component Docs (Link, Do Not Copy)

- LVGL: [managed_components/lvgl__lvgl/README.md](managed_components/lvgl__lvgl/README.md)
- AVI player: [managed_components/espressif__avi_player/README.md](managed_components/espressif__avi_player/README.md)
- ESP codec dev: [managed_components/espressif__esp_codec_dev/README.md](managed_components/espressif__esp_codec_dev/README.md)
- ESP new JPEG: [managed_components/espressif__esp_new_jpeg/README.md](managed_components/espressif__esp_new_jpeg/README.md)

## Quick Change Map

- Add or update board peripheral behavior: [components/port_bsp](components/port_bsp)
- Add UI screens/widgets: [components/ui_bsp](components/ui_bsp) and [components/user_app](components/user_app)
- Adjust app flow/tasks/events: [components/user_app/user_app.cpp](components/user_app/user_app.cpp)
- Add managed dependencies: [main/idf_component.yml](main/idf_component.yml)