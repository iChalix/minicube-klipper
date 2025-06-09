# Configuration Guide

## Overview

This guide explains the key configuration settings in the Klipper setup and how to customize them for your specific printer.

## Core Configuration Files

### printer.cfg Structure

The main configuration file is organized into logical sections:

1. **MCU and Basic Settings**
2. **Kinematics and Motion**
3. **Stepper Configurations**
4. **Extruder and Thermistor**
5. **Heated Bed**
6. **Fan Control**
7. **TMC2209 Driver Settings**
8. **Print Macros**

## Critical Settings to Verify

### MCU Serial Connection

```ini
[mcu]
serial: /dev/serial/by-id/usb-Klipper_lpc1768_1690FF08610039AF39E9F5-if00
```

**Important**: Update this path to match your specific controller board's USB ID.

To find your device:
```bash
ls /dev/serial/by-id/
```

### Virtual SD Card Path

```ini
[virtual_sdcard]
path: /home/tim/printer_data/gcodes
```

**Important**: Update this path to match your host system's gcode directory.

## Motion and Kinematics

### CoreXY Configuration

```ini
[printer]
kinematics: corexy
max_velocity: 300
max_accel: 2000
max_z_velocity: 5
max_z_accel: 100
```

**Tuning Notes**:
- Start with conservative acceleration values
- Increase gradually after testing
- Z-axis limits are intentionally low for precision

### Stepper Motor Settings

#### Rotation Distance Calculation

For X/Y axes (belt drive):
```
rotation_distance = (belt_pitch × pulley_teeth) / microsteps
Current setting: 32mm (20T pulley, 2mm belt pitch)
```

For Z-axis (lead screw):
```
rotation_distance = lead_screw_pitch
Current setting: 8mm
```

For Extruder:
```
rotation_distance = (π × hobbed_gear_diameter) / gear_ratio
Current setting: 7.78mm (calibrated value)
```

## Sensorless Homing Configuration

### TMC2209 Sensitivity

```ini
[tmc2209 stepper_x]
driver_SGTHRS: 70
```

**Tuning SGTHRS**:
- Higher values = less sensitive (harder to trigger)
- Lower values = more sensitive (easier to trigger)
- Range: 0-255
- Start with 70, adjust if homing fails or triggers early

### Homing Speeds

```ini
[stepper_x]
homing_speed: 30
homing_retract_dist: 0
```

**Notes**:
- Sensorless homing requires consistent speed
- `homing_retract_dist: 0` prevents second homing move
- Speed affects sensitivity - slower = more sensitive

## Thermistor Configuration

### Custom NTC3950 Definition

```ini
[thermistor my_NTC3950]
temperature1: 25
resistance1: 100000
temperature2: 150
resistance2: 1791
temperature3: 250
resistance3: 283
```

**Calibration Process**:
1. Measure resistance at known temperatures
2. Use at least 3 data points
3. Verify readings against known good thermometer

## Pressure Advance Tuning

### Current Settings

```ini
[extruder]
pressure_advance: 0.025
pressure_advance_smooth_time: 0.020
```

**Tuning Process**:
1. Print pressure advance test pattern
2. Measure corner accuracy
3. Adjust in 0.005 increments
4. Direct drive typically uses 0.02-0.06 range

## PID Tuning

### Auto-Generated Values

The `SAVE_CONFIG` section contains calibrated PID values:

```ini
[extruder]
control = pid
pid_kp = 28.951
pid_ki = 1.520
pid_kd = 137.879

[heater_bed]
control = pid
pid_kp = 67.355
pid_ki = 2.291
pid_kd = 495.058
```

**Re-tuning When Needed**:
- After changing hotend/thermistor
- If temperature oscillates
- After major hardware changes

Commands:
```
PID_CALIBRATE HEATER=extruder TARGET=200
PID_CALIBRATE HEATER=heater_bed TARGET=60
```

## TFT Bridge Macros

### Filament Management

```ini
[gcode_macro M701]
description: Load filament (uses existing macro if available)
gcode:
    {% if printer['gcode_macro LOAD_FILAMENT'] is defined %}
        LOAD_FILAMENT {rawparams}
    {% else %}
        TFT_LOAD_FILAMENT {rawparams}
    {% endif %}
```

**Customization**:
- Replace `TFT_LOAD_FILAMENT` with your preferred macro
- Adjust speeds and distances in fallback macros
- Add custom parameters as needed

### Preheat Presets

```ini
[gcode_macro TFT_PREHEAT_PLA]
description: Preheat for PLA
gcode:
    M104 S200      # Set hotend to 200C
    M140 S60       # Set bed to 60C
```

**Material Settings**:
- PLA: 200°C hotend, 60°C bed
- PETG: 240°C hotend, 80°C bed
- ABS: 250°C hotend, 100°C bed

Adjust based on your specific filaments.

## Print Start/End Sequences

### START_PRINT Macro

Key parameters:
- `BED_TEMP`: Bed temperature
- `EXTRUDER_TEMP`: Hotend temperature

**Customization Points**:
1. Add bed mesh loading: `BED_MESH_PROFILE LOAD=default`
2. Modify purge line length/position
3. Add additional warm-up time
4. Include chamber heating for ABS

### END_PRINT Macro

**Safety Features**:
- Retracts filament to prevent oozing
- Parks toolhead away from print
- Turns off all heaters
- Disables steppers

## Fan Control

### Hotend Fan (Temperature Controlled)

```ini
[heater_fan hotend_fan]
pin: P2.4
heater: extruder
heater_temp: 50.0
```

**Notes**:
- Activates when extruder reaches 50°C
- Runs at full speed when active
- Critical for hotend cooling

### Controller Fan

```ini
[controller_fan my_controller_fan]
pin: P2.0
stepper: stepper_x,stepper_y,stepper_z,extruder
fan_speed: 0.6
```

**Purpose**:
- Cools stepper drivers during operation
- Runs at 60% speed when any stepper is active
- Helps prevent driver overheating

## Safety Limits

### Temperature Limits

```ini
[extruder]
min_temp: 5
max_temp: 280

[heater_bed]
min_temp: 5
max_temp: 130
```

**Important**:
- Set max temps below component limits
- Min temp prevents false readings
- Klipper will shut down if limits exceeded

### Motion Limits

```ini
[stepper_x]
position_min: -1
position_max: 120
```

**Notes**:
- Negative min allows slight overtravel
- Max position should match physical limits
- Software limits prevent crashes

## Advanced Features

### Input Shaping (Optional)

To add resonance compensation:
1. Install accelerometer (ADXL345)
2. Run resonance tests
3. Configure input shaping parameters

### Bed Mesh (Optional)

To add automatic bed leveling:
1. Install probe (BLTouch, inductive, etc.)
2. Configure probe settings
3. Enable bed mesh macros

### Multiple Extruders (Future)

The configuration includes commented sections for dual extruder setup. Uncomment and configure as needed for multi-material printing.