# MiniCube Klipper Configuration

A complete Klipper 3D printer configuration for a CoreXY printer with TFT display compatibility.

## Overview

This repository contains Klipper configuration files for a custom CoreXY 3D printer with the following specifications:

- **Build Volume**: 120mm × 120mm × 120mm
- **Kinematics**: CoreXY
- **Controller**: LPC1768-based board
- **Stepper Drivers**: TMC2209 with UART communication
- **Homing**: Sensorless X/Y, optical Z endstop
- **Extruder**: Direct drive with custom NTC3950 thermistor
- **Display**: TFT display with bridge compatibility

## Quick Start

1. **Install Klipper** on your host system (Raspberry Pi, etc.)
2. **Flash firmware** to your LPC1768 controller board
3. **Copy configuration files** to your Klipper config directory:
   ```bash
   cp printer.cfg /home/pi/printer_data/config/
   cp klipper_tft_macros.cfg /home/pi/printer_data/config/
   ```
4. **Update paths** in `printer.cfg`:
   - Update MCU serial path in `[mcu]` section
   - Update virtual SD card path in `[virtual_sdcard]` section
5. **Restart Klipper** service

## Configuration Files

### `printer.cfg`
Main configuration file containing:
- Hardware definitions (steppers, heaters, fans)
- TMC2209 stepper driver settings
- Custom thermistor configuration
- Core print macros (START_PRINT, END_PRINT, etc.)
- Sensorless homing configuration

### `klipper_tft_macros.cfg`
TFT display compatibility macros that translate Marlin-style G-codes to Klipper equivalents:
- Filament management (M701/M702)
- Bed leveling commands (M420/M421)
- Probe control (M280, M401/M402)
- PID tuning (M303)
- Settings management (M500/M503)

## Key Features

### Sensorless Homing
- X and Y axes use TMC2209 sensorless homing
- Configured with `driver_SGTHRS: 70` for reliable detection
- Z-axis uses optical endstop sensor

### Direct Drive Extruder
- Custom pressure advance settings (0.025)
- NTC3950 thermistor with custom temperature curve
- Optimized for direct drive configuration

### TFT Display Support
- Full compatibility with TFT displays via bridge macros
- Automatic fallback to built-in macros if custom ones don't exist
- Support for common TFT functions and presets

## Calibration

After initial setup, perform these calibrations:

1. **Stepper Direction**: Verify all motors move in correct directions
2. **Endstop Testing**: Test sensorless homing sensitivity
3. **PID Tuning**: Run PID calibration for hotend and bed
4. **Pressure Advance**: Fine-tune for your filament
5. **Z-Offset**: Set proper first layer height

## Usage

### Starting a Print
The `START_PRINT` macro handles:
- Homing all axes
- Heating bed and extruder to target temperatures
- Purge line for consistent first layer

### TFT Display Functions
Access common functions through your TFT display:
- Filament load/unload (M701/M702)
- Preheat presets (PLA, PETG, ABS)
- Bed leveling controls
- PID tuning

## Safety Notes

- Always verify thermistor readings before printing
- Check endstop functionality after any changes
- Monitor first layers closely with new configurations
- Keep firmware and configuration files backed up

## Support

For issues or questions:
- Check the troubleshooting section in docs/
- Review Klipper documentation
- Verify hardware connections and settings

## License

This configuration is provided as-is for educational and personal use.