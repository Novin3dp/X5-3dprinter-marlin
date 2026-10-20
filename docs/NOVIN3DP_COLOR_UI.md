# Novin3dp Cosmic Cyan theme for Marlin TFT Color UI

This implementation selects the Novin3dp Cosmic Cyan theme by default and converts core TFT icons to 1-bpp bitmaps. It does not replace Marlin menu/action logic or change motion, thermal, probing, SD-card, or print-control behavior.

## Hardware target

- MKS Robin Nano V3.x family (STM32F407)
- MKS TS35 when connected as a native Marlin TFT Color UI display
- Deep navy background, cyan/ice-blue accents, restrained amber/red for heat and warning states

## Enable the theme

The fallback in `Marlin/src/lcd/tft/ui_theme.h` is now `NOVIN3DP`, so the theme is selected unless the build explicitly overrides `TFT_THEME`. The display driver and UI must still be configured for the actual board/display. If the configuration does not use `TFT_COLOR_UI`, this theme file will not affect the active interface.

## Memory approach

The theme uses RGB565 color constants and existing Marlin drawing primitives. It adds no full-screen background image, animation, framebuffer, or extra font, so the theme itself adds effectively no runtime image-buffer overhead.

The current canvas renderer already supports `GREYSCALE1`, so selected existing image assets have been converted from 4-bpp to 1-bpp without adding a renderer. This conversion preserves the original image dimensions and uses palette index 0 / index 8 as transparent/background pixels. Some icons and decorative button assets remain in their original formats and are not yet converted.

Raw monochrome bitmap storage, excluding alignment/metadata:

- 24x24: 72 bytes
- 32x32: 128 bytes
- 48x48: 288 bytes
- 64x64: 512 bytes

Converted assets currently include hotend, heated bed, fan, SD, menu, settings, confirm/cancel, increase/decrease, pause, feed/flow rate, directory/back, refresh/leveling, and elapsed/remaining-time icons. A 64x64 icon now occupies 512 bytes instead of 2,048 bytes; a 32x32 icon occupies 128 bytes instead of 512 bytes.

## Design intent

- Keep temperature, bed, fan, XYZ coordinates and print status primary on the status page.
- Make Print/Files, Prepare/Move, Temperature, Leveling and Settings the main actions.
- Keep Pause/Resume and Stop explicit during printing.
- Use no full-screen decorative bitmap for the cosmic background.
- Use red for hotend/stop, amber for bed heat, cyan for normal controls and mint for positive/confirmed states.

## Validation status

This is an initial UI implementation and has not been compiled against a specific machine configuration or tested on physical TS35 hardware. The home icon asset was not converted because its source array does not match the expected raw 4-bpp byte count; it remains unchanged. Before enabling it on a production printer, build with the exact configuration and verify touch calibration, display orientation, temperatures and print-control actions.