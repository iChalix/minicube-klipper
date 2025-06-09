# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Klipper 3D printer configuration repository for a CoreXY printer with the following characteristics:
- CoreXY kinematics with 120x120x120mm build volume
- LPC1768-based controller board
- TMC2209 stepper drivers with sensorless homing on X/Y axes
- Direct drive extruder with custom NTC3950 thermistor
- TFT display compatibility via bridge macros

## Configuration Architecture

### Main Configuration Files

- `printer.cfg` - Primary Klipper configuration containing all hardware definitions, stepper settings, thermistor configurations, and core print macros
- `klipper_tft_macros.cfg` - TFT display bridge compatibility macros that translate Marlin-style G-codes to Klipper equivalents

### Key Hardware Configuration

The printer uses TMC2209 drivers with:
- Sensorless homing on X/Y axes (using `virtual_endstop` and SGTHRS values)
- Optical Z-endstop sensor
- UART communication for all stepper drivers
- Custom thermistor definition (`my_NTC3950`) for the extruder

### Macro System

Core print macros include:
- `START_PRINT` - Handles bed/extruder heating, homing, and purge line
- `END_PRINT` - Parks toolhead, turns off heaters, retracts filament
- `PAUSE`/`RESUME`/`CANCEL_PRINT` - Standard print control
- `M600` - Filament change macro

TFT bridge macros provide compatibility with TFT displays by translating:
- M701/M702 → filament load/unload
- M420/M421 → bed leveling commands  
- M851 → Z-offset adjustment
- M303 → PID tuning
- Various BLTouch commands (M280, M401, M402)

## Configuration Notes

- The extruder uses pressure advance (0.025) tuned for direct drive
- Z-axis uses optical endstop, X/Y use sensorless homing
- Bed mesh leveling commands are supported via bridge macros
- PID values are auto-generated and stored in SAVE_CONFIG section
- The configuration includes references to `fluidd.cfg` (external file)

## Development Guidelines

When modifying configurations:
1. Hardware changes should be made in `printer.cfg`
2. TFT compatibility additions should go in `klipper_tft_macros.cfg`
3. Always test stepper motor directions and endstop functionality after changes
4. Verify temperature readings after thermistor modifications
5. Use `SAVE_CONFIG` to persist calibrated values (PID, bed mesh, etc.)