# Hardware Documentation

## Printer Specifications

### Frame and Motion System
- **Type**: CoreXY kinematics
- **Build Volume**: 120mm × 120mm × 120mm
- **Frame**: Aluminum extrusion construction
- **Motion**: Belt-driven X/Y, lead screw Z-axis

### Controller Board
- **MCU**: LPC1768-based controller
- **Serial Connection**: USB (update path in config)
- **Stepper Drivers**: TMC2209 with UART communication
- **Voltage**: 12V/24V power supply

## Stepper Motor Configuration

### X-Axis Stepper (stepper_x)
- **Step Pin**: P2.2
- **Direction Pin**: P2.6
- **Enable Pin**: !P2.1
- **Microsteps**: 16
- **Rotation Distance**: 32mm
- **Position Range**: -1mm to 120mm
- **Homing**: Sensorless (TMC2209 virtual endstop)
- **Current**: 0.8A run, 0.25A hold

### Y-Axis Stepper (stepper_y)
- **Step Pin**: P0.19
- **Direction Pin**: P0.20
- **Enable Pin**: !P2.8
- **Microsteps**: 16
- **Rotation Distance**: 32mm
- **Position Range**: -9mm to 120mm
- **Homing**: Sensorless (TMC2209 virtual endstop)
- **Current**: 0.8A run, 0.25A hold

### Z-Axis Stepper (stepper_z)
- **Step Pin**: P0.22
- **Direction Pin**: P2.11
- **Enable Pin**: !P0.21
- **Microsteps**: 16
- **Rotation Distance**: 8mm (lead screw pitch)
- **Position Range**: 0mm to 120mm
- **Endstop**: Optical sensor on P1.25
- **Current**: 0.65A run, 0.25A hold

## Extruder System

### Extruder Motor
- **Step Pin**: P2.13
- **Direction Pin**: P0.11
- **Enable Pin**: !P2.12
- **Microsteps**: 16
- **Rotation Distance**: 7.78mm (calibrated)
- **Current**: 0.5A run (TMC2209)
- **Type**: Direct drive configuration

### Hotend
- **Nozzle Diameter**: 0.4mm
- **Filament Diameter**: 1.75mm
- **Heater Pin**: P2.7
- **Thermistor**: Custom NTC3950
- **Sensor Pin**: P0.24
- **Temperature Range**: 5°C to 280°C
- **Pressure Advance**: 0.025 (tuned for direct drive)

### Custom Thermistor (my_NTC3950)
Calibration points:
- 25°C: 100,000Ω
- 150°C: 1,791Ω
- 250°C: 283Ω

## Heated Bed

- **Heater Pin**: P2.5
- **Thermistor**: Generic 3950
- **Sensor Pin**: P0.23
- **Temperature Range**: 5°C to 130°C
- **Size**: 120mm × 120mm

## Fan Configuration

### Part Cooling Fan
- **Pin**: P2.3
- **Type**: Variable speed PWM control
- **Voltage**: 12V/24V

### Hotend Fan
- **Pin**: P2.4
- **Type**: Temperature controlled
- **Activation**: 50°C+ on extruder
- **Voltage**: 12V/24V

### Controller Fan
- **Pin**: P2.0
- **Type**: Multi-stepper controlled
- **Speed**: 60% when any stepper active
- **Controlled Steppers**: X, Y, Z, Extruder

## TMC2209 Stepper Driver Settings

All drivers configured for UART communication with the following pins:

### Driver Connections
- **X Driver UART**: P1.17
- **Y Driver UART**: P1.15
- **Z Driver UART**: P1.10
- **E Driver UART**: P1.8

### Sensorless Homing (X/Y Only)
- **X Diag Pin**: P1.29
- **Y Diag Pin**: P1.27
- **Sensitivity**: SGTHRS = 70
- **StealthChop**: Disabled for homing accuracy

### Current Settings
- **X/Y Motors**: 0.8A run, 0.25A hold
- **Z Motor**: 0.65A run, 0.25A hold
- **Extruder**: 0.5A run, StealthChop enabled

## Endstop Configuration

### X/Y Axes (Sensorless)
- Uses TMC2209 stallGuard feature
- No physical endstop switches required
- Sensitivity tuned via SGTHRS parameter

### Z-Axis (Optical)
- **Pin**: P1.25 (pullup enabled)
- **Type**: Optical sensor
- **Position**: Z-min position
- **Homing Speed**: 8mm/s initial, 2mm/s precision

## Wiring Notes

### Power Requirements
- **Main Power**: 12V or 24V (verify heater compatibility)
- **Logic Power**: 5V from USB or dedicated supply
- **Stepper Current**: Configure based on motor specifications

### Signal Connections
- All stepper enable pins are inverted (!)
- TMC UART requires proper ground connections
- Thermistor connections require twisted pair wiring

## Recommended Upgrades

### Optional Enhancements
- **Bed Leveling**: Add BLTouch or inductive probe
- **Enclosure**: Temperature control for ABS printing
- **Lighting**: LED strips for better visibility
- **Camera**: OctoPrint/Mainsail webcam integration

### Performance Tuning
- **Input Shaping**: Add accelerometer for resonance compensation
- **Linear Advance**: Further pressure advance calibration
- **Cooling**: Upgrade part cooling fan for overhangs