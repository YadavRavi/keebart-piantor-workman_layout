# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a ZMK firmware configuration repository for custom split keyboards. It defines board configurations and keymaps for three keyboards:
- **Piantor Pro BT** - 36/42 key split keyboard
- **Corne Choc Pro** - 36/42 key split keyboard
- **Sofle Choc Pro** - split keyboard with encoders

All boards use the nRF52840 SoC with Bluetooth and USB connectivity, nice!view display support, and RGB underglow.

## Build System

Firmware builds automatically via GitHub Actions on push/PR. The workflow uses ZMK's official `build-user-config.yml` (pinned to v0.3).

The build matrix is defined in `build.yaml`. Each entry specifies:
- `board`: Target board (e.g., `piantor_pro_bt_left`)
- `shield`: Display shield (e.g., `nice_view`, `settings_reset`)
- `snippet`: `studio-rpc-usb-uart` for ZMK Studio support
- `cmake-args`: `-DCONFIG_ZMK_STUDIO=y` for left halves

To add a new build variant, add an entry to `build.yaml` under `include:`.

## Repository Structure

```
config/              # User keymap configurations
  ├── <board>.keymap # Keymap definitions
  ├── <board>.json   # ZMK Studio metadata
  └── west.yml       # West manifest (pins ZMK v0.3)

boards/
  └── arm/           # Board definitions
      └── <board>/
          ├── <board>.dtsi        # Shared device tree
          ├── <board>_left.dts    # Left half device tree
          ├── <board>_right.dts   # Right half device tree
          ├── <board>_left_defconfig   # Left Kconfig
          ├── <board>_right_defconfig  # Right Kconfig
          ├── <board>-layouts.dtsi     # Physical layout definitions
          └── <board>.keymap           # Default keymap

  └── shields/
      └── nice_view_disp/   # Custom nice!view display widget
```

## Key Configuration Files

**Keymaps**: Edit `config/<board>.keymap` to change key bindings. Uses ZMK keymap syntax with `#include <behaviors.dtsi>` and `#include <dt-bindings/zmk/keys.h>`.

**Board Config**: `boards/arm/<board>/<board>_left_defconfig` and `*_right_defconfig` contain Kconfig options for features like:
- `CONFIG_ZMK_RGB_UNDERGLOW=y` - RGB LEDs
- `CONFIG_ZMK_STUDIO=y` - ZMK Studio support
- `CONFIG_ZMK_BLE_EXPERIMENTAL_CONN=y` - Improved BLE stability

**Device Tree**: `<board>.dtsi` defines hardware (GPIO pins, SPI/I2C buses, matrix transforms, battery fuel gauge).

## Display Configuration

The `nice_view_disp` shield provides a custom vertical widget for the nice!view display. To use the built-in ZMK widget instead, add to the board's `.conf`:
```
CONFIG_ZMK_DISPLAY_STATUS_SCREEN_BUILT_IN=y
```

To rotate 180 degrees:
```
CONFIG_NICE_VIEW_DISP_ROTATE_180=y
```

## ZMK Studio

All boards have ZMK Studio enabled. The left half includes `&studio_unlock` in the keymap (lower layer) to unlock Studio features at runtime.
