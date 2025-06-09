# Troubleshooting Guide

## Common Issues and Solutions

### Startup and Connection Issues

#### "MCU Connection Lost" Error

**Symptoms**: Klipper reports MCU disconnection or timeout

**Solutions**:
1. **Check USB Connection**
   - Verify USB cable is properly connected
   - Try a different USB port or cable
   - Check for loose connections

2. **Verify Serial Path**
   ```bash
   ls /dev/serial/by-id/
   ```
   Update `printer.cfg` with correct device path

3. **Check Firmware**
   - Ensure correct Klipper firmware is flashed to LPC1768
   - Verify firmware build configuration matches hardware

4. **Power Issues**
   - Check 12V/24V power supply connections
   - Verify controller board power LED is on
   - Ensure adequate power supply capacity

#### "Config File Error" on Startup

**Common Causes**:
- Syntax errors in configuration files
- Missing or incorrect pin assignments
- Invalid parameter values

**Debugging Steps**:
1. Check Klipper log: `tail -f /tmp/klippy.log`
2. Validate configuration syntax
3. Comment out recent changes to isolate issue
4. Verify all pin assignments match board pinout

### Homing Issues

#### Sensorless Homing Fails

**X/Y Axis Won't Home**:

1. **Check SGTHRS Sensitivity**
   - Too high (>100): Motor may not trigger stallguard
   - Too low (<50): May trigger prematurely
   - Adjust in steps of 10: `driver_SGTHRS: 70`

2. **Verify Motor Current**
   - Insufficient current prevents stallguard detection
   - Check TMC2209 run_current settings
   - Ensure drivers are properly powered

3. **Mechanical Issues**
   - Belt tension too loose or tight
   - Binding in motion system
   - Debris on rails or pulleys

**Testing Commands**:
```
G28 X    # Home X only
G28 Y    # Home Y only
```

#### Z-Axis Homing Problems

**Optical Endstop Not Triggering**:

1. **Check Wiring**
   - Verify P1.25 connection
   - Ensure pullup resistor is enabled: `endstop_pin: ^P1.25`
   - Test with multimeter for continuity

2. **Sensor Alignment**
   - Clean optical sensor lens
   - Verify flag/interrupter alignment
   - Check for proper gap distance

3. **Test Endstop Status**
   ```
   QUERY_ENDSTOPS
   ```
   Should show `open` when not triggered, `TRIGGERED` when blocked

### Temperature Issues

#### Thermistor Reading Errors

**"ADC out of range" Error**:
- Check thermistor wiring for shorts or breaks
- Verify correct thermistor type in configuration
- Ensure proper crimped connections

**Temperature Readings Incorrect**:
1. **Verify Thermistor Type**
   - Confirm NTC3950 specifications
   - Check custom thermistor calibration points
   - Compare with known good thermometer

2. **Check Electrical Connections**
   - Inspect for loose crimps
   - Verify shielded cable grounding
   - Test resistance with multimeter

#### PID Tuning Issues

**Temperature Oscillations**:
- Run PID calibration: `PID_CALIBRATE HEATER=extruder TARGET=200`
- Check for adequate cooling/heating capacity
- Verify proper thermistor placement

**Slow Heating**:
- Check heater cartridge resistance
- Verify power supply capacity
- Inspect for loose connections

### Stepper Motor Problems

#### Motors Not Moving

1. **Check Enable Pins**
   - Verify `enable_pin: !P2.1` (note inversion)
   - Test with simple movement commands

2. **TMC2209 Driver Issues**
   - Check UART communication
   - Verify driver power and ground
   - Inspect for proper driver installation

3. **Test Movement**
   ```
   G91          # Relative mode
   G1 X10 F600  # Move X 10mm
   G1 Y10 F600  # Move Y 10mm
   G1 Z5 F300   # Move Z 5mm
   ```

#### Wrong Direction Movement

**Quick Fix**:
- Add or remove `!` from dir_pin in stepper configuration
- Example: `dir_pin: P2.6` → `dir_pin: !P2.6`

#### Skipping Steps

**Causes and Solutions**:
1. **Current Too Low**: Increase TMC2209 run_current
2. **Speed Too High**: Reduce max_velocity or acceleration
3. **Mechanical Binding**: Check for obstructions
4. **Belt Tension**: Adjust belt tightness

### Print Quality Issues

#### First Layer Problems

**Not Sticking**:
- Adjust Z-offset lower (negative direction)
- Clean bed surface thoroughly
- Verify bed temperature for material

**Too Close**:
- Adjust Z-offset higher (positive direction)
- Check for bed warping
- Verify nozzle cleanliness

#### Extrusion Issues

**Under-Extrusion**:
1. Check extruder calibration (rotation_distance)
2. Verify proper temperature for filament
3. Clean or replace nozzle
4. Check for filament binding

**Over-Extrusion**:
1. Reduce flow rate in slicer
2. Verify rotation_distance calculation
3. Check pressure advance settings

### TFT Display Issues

#### Commands Not Working

**TFT Bridge Macros**:
- Verify `klipper_tft_macros.cfg` is included
- Check macro syntax and indentation
- Test individual macros from console

**Connection Problems**:
- Check TFT serial connection
- Verify baud rate settings
- Ensure TFT firmware supports Klipper

### Performance Optimization

#### Slow Print Speeds

1. **Increase Acceleration**
   - Gradually increase max_accel from 2000
   - Test with simple shapes first
   - Monitor for skipping or artifacts

2. **Tune Jerk Settings**
   - Increase square_corner_velocity
   - Adjust maximum_z_velocity for faster Z moves

3. **Optimize Pressure Advance**
   - Fine-tune for better corners at speed
   - Test with different values: 0.02-0.05 range

#### Print Artifacts

**Ringing/Ghosting**:
- Reduce acceleration or jerk
- Check belt tension
- Consider input shaping implementation

**Layer Shifts**:
- Check motor current settings
- Verify belt tension
- Inspect for mechanical binding

## Diagnostic Commands

### Status Checking
```
FIRMWARE_RESTART     # Restart Klipper firmware
STATUS              # Show printer status
QUERY_ENDSTOPS      # Check endstop states
GET_POSITION        # Show current position
```

### Temperature Monitoring
```
M105                # Get temperatures
PID_CALIBRATE HEATER=extruder TARGET=200
PID_CALIBRATE HEATER=heater_bed TARGET=60
```

### Motion Testing
```
G28                 # Home all axes
G1 X60 Y60 Z30 F3000  # Move to center
M84                 # Disable steppers
```

### TMC Driver Information
```
DUMP_TMC STEPPER=stepper_x    # Show TMC2209 registers
```

## Emergency Procedures

### Emergency Stop
- Physical emergency stop button (if installed)
- Send `M112` command immediately
- Power cycle printer if unresponsive

### Thermal Runaway
- Klipper automatically shuts down on thermal runaway
- Check thermistor connections before restart
- Verify heater functionality

### Power Loss Recovery
- Klipper doesn't have built-in power loss recovery
- Consider UPS for critical prints
- Save G-code state periodically for manual recovery

## Getting Help

When seeking support, provide:
1. **Error Messages**: Complete Klipper log output
2. **Configuration**: Relevant sections of printer.cfg
3. **Hardware Details**: Specific components and wiring
4. **Steps to Reproduce**: Exact sequence that causes issue
5. **Environmental Info**: Temperature, humidity, recent changes

### Log File Locations
- **Klipper Log**: `/tmp/klippy.log`
- **System Log**: `journalctl -u klipper`
- **OctoPrint Log**: `~/.octoprint/logs/`

### Useful Commands for Support
```bash
# Get last 50 lines of Klipper log
tail -50 /tmp/klippy.log

# Monitor live log
tail -f /tmp/klippy.log

# Check system status
systemctl status klipper
```