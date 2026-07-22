# CubeX Trio RepRap Conversion

## Overview

This project modernizes a discontinued **3D Systems CubeX Trio** by replacing its closed, manufacturer-controlled hardware and software with widely supported, open-source 3D printer components.

The original CubeX motherboard, hotend, extruder, and proprietary filament system are being replaced with modern parts that can run **Marlin**, an open-source firmware used to control many custom and commercial 3D printers.

The printer is initially being rebuilt as a **single-extruder Bowden printer**. This means it will print with one material at a time, while the motor that pushes the filament is mounted away from the moving printhead. The filament travels through a tube to the hotend, where it is melted and deposited onto the print bed.

The design may later be expanded to support additional extruders.

The conversion follows the general **RepRap** approach. RepRap is an open-source 3D printing platform based on standardized, replaceable, and well-documented components. This makes the printer easier to repair, upgrade, and modify without relying on discontinued or manufacturer-specific parts.

## Main Goals

- Replace the proprietary CubeX electronics
- Run open-source Marlin firmware
- Use standard 1.75 mm filament
- Install a modern V6-style hotend
- Preserve as much of the original CubeX frame and motion system as possible
- Restore as much of the original build volume as possible
- Add automatic bed leveling
- Reuse original CubeX parts where practical
- Eventually control the printer without requiring a connected computer

## Hardware

### Installed or Purchased

- 3D Systems CubeX Trio frame and motion system
- BIGTREETECH SKR V1.4 Turbo
- LPC1769 processor
- 5 x TMC2209 V1.3 stepper drivers
- Redrex S-360-24 24 V power supply
- V6-style 24 V hotend
- 24 V heater cartridge
- 100K hotend thermistor
- Dual-gear Bowden extruder
- PTFE Bowden tubing
- PC4-M6 and PC4-M10 pneumatic fittings
- 24 V 3010 hotend heatsink fan
- 24 V 5015 part-cooling blower
- LJ12A3-4-Z/BX inductive proximity sensor
- 24 V to 5 V optocoupler isolation board
- External heated-bed MOSFET module
- 18 AWG silicone wire
- 22 to 26 AWG silicone wire
- Dupont connector and crimping kit

### Original CubeX Parts Being Reused

- Aluminum frame
- Linear motion system
- X, Y, and Z stepper motors
- Dual Y-axis motors
- Glass print surface
- Aluminum bed plate
- Original endstops where compatible
- Original aluminum carriage components
- Three original 12 V part-cooling fans
- Original front display, pending investigation

### Not Yet Permanently Installed

- Inductive probe
- Original CubeX screen
- Original 12 V part-cooling fans
- Permanent custom hotend carriage
- Heated-bed system

## Original Printer Disassembly

The following work has been completed:

- Removed the original CubeX motherboard
- Preserved the original wiring connectors
- Removed the original proprietary hotend and extruder components
- Removed panels as needed to access the electronics and motion system
- Inspected the original motors, fans, endstops, bed, and carriage hardware
- Retained original CubeX aluminum carriage parts for modification or reuse

## Controller Installation

The original CubeX motherboard was replaced with a BIGTREETECH SKR V1.4 Turbo.

The board is running Marlin using the NXP LPC1769 build environment.

### Stepper Driver Assignments

| Driver Socket | Function |
|---|---|
| X | X-axis motor |
| Y | First Y-axis motor |
| Z | Z-axis motor |
| E0 | Extruder motor |
| E1 | Second Y-axis motor |

The two Y motors are controlled together using Marlin's dual-Y configuration.

### TMC2209 Setup

Five TMC2209 V1.3 drivers are installed.

The DIAG pins were removed because sensorless homing is not currently being used.

TMC2209 UART communication has been successfully configured and tested.

## Firmware

### Firmware Version

Marlin 2.1.1.6

### Build Target

LPC1769

### USB Communication Fix

USB communication initially did not work correctly.

The following setting in `Configuration.h` was changed from:

```cpp
#define SERIAL_PORT 0
```

to:

```cpp
#define SERIAL_PORT -1
```

After recompiling and flashing the firmware, USB communication worked correctly.

## Motion System

The following motion functions have been tested successfully:

- X-axis movement
- Y-axis movement
- Dual Y-axis motor movement
- Z-axis movement
- X endstop
- Y endstop
- X homing
- Y homing

### Still Pending

- Z endstop or probe installation
- Z homing
- Final travel limits
- Final printable-area configuration
- X, Y, and Z steps-per-millimetre verification

## Hotend Installation

A 24 V V6-style hotend has been installed.

The current hotend assembly includes:

- V6-style heatsink
- Heater block
- 24 V heater cartridge
- 100K thermistor
- 0.4 mm nozzle
- 24 V 3010 heatsink fan
- Bowden tube connection

The thermistor is connected to `TH0`.

The heater cartridge is connected to `HE0`.

## Thermistor Testing

The thermistor was tested using:

```gcode
M105
```

A normal room-temperature reading was observed.

The thermistor was also warmed by hand, and the reported temperature increased correctly.

At one point, Marlin reported:

```text
T:-15.00
Error: MINTEMP triggered
```

This was caused by the thermistor becoming disconnected.

After reconnecting the thermistor, normal temperature readings returned.

## Heater Testing

The heater cartridge resistance was measured as 10.7 ohms.

At 24 V, this is consistent with approximately 54 W of heater power.

The heater was connected to HE0 and successfully heated the hotend.

Marlin thermal protection remained enabled during testing.

## PID Tuning

PID tuning was performed at 200 C using:

```gcode
M303 E0 S200 C8
```

The resulting PID values were:

```cpp
#define DEFAULT_Kp 14.69
#define DEFAULT_Ki 1.46
#define DEFAULT_Kd 36.84
```

These values were entered into `Configuration.h`, after which the firmware was rebuilt and flashed.

### PID Tuning Result

The hotend now:

- Heats smoothly
- Overshoots by only approximately 7 C
- Stabilizes near 200 C
- Holds temperature within approximately +/-0.5 C

## Hotend Fan

The 24 V hotend heatsink fan was connected to FAN1.

The fan initially appeared not to work, but its polarity was reversed.

After correcting the polarity, the fan operated correctly from FAN1.

### Important Note

Brushless DC fans are polarity-sensitive.

The heater cartridge and thermistor are not polarity-sensitive, but the fans are.

## Extruder

The dual-gear Bowden extruder and extruder stepper motor were mounted and connected to E0.

Cold extrusion protection initially prevented testing:

```text
echo: cold extrusion prevented
```

The extruder was then tested after heating the hotend.

The motor initially rotated in the wrong direction.

The E0 direction was corrected by changing the value of:

```cpp
#define INVERT_E0_DIR
```

After rebuilding and flashing Marlin, the extruder fed filament toward the hotend correctly.

### Extruder Status

- Extruder motor communication works
- Extruder motor direction is correct
- Dual-gear mechanism operates
- Bowden feed direction is correct
- E-steps calibration is still required
- Final filament extrusion test is pending arrival of filament

## E-Steps Calibration

E-steps have not yet been calibrated.

The planned procedure is:

1. Heat the hotend.
2. Mark the filament 120 mm above the extruder.
3. Command 100 mm of extrusion:

```gcode
G91
G1 E100 F100
```

4. Measure the actual amount of filament moved.
5. Calculate the corrected E-steps value:

```text
New E-steps = Current E-steps x 100 / Actual filament moved
```

6. Apply the result:

```gcode
M92 E<new_value>
M500
```

The final value will also be added permanently to `Configuration.h`.

## Current Carriage Problem

The V6 hotend and extruder have been mounted temporarily.

The current mount places the nozzle too far away from the original CubeX nozzle position. As a result, the nozzle can only reach approximately half of the print bed.

Firmware offsets cannot recover an area that the nozzle cannot physically reach.

### Required Solution

A custom carriage or modified aluminum adapter must position the new nozzle as close as possible to the original nozzle location.

The final carriage should:

- Attach securely to the original CubeX X carriage
- Support the V6 hotend
- Place the nozzle near the original X and Y position
- Lower the shorter V6 hotend enough to reach the bed
- Avoid collisions with the CubeX frame
- Preserve maximum X and Y travel
- Support the hotend heatsink fan
- Support part-cooling fans
- Leave room for a Z probe
- Prevent the hotend from rotating or moving vertically

### Planned Fabrication Method

The existing aluminum mount may be modified using:

- Fine-tooth hacksaw
- Metal files
- HSS drill bits
- Step drill bit
- Rotary tool with reinforced cutting discs
- V6 groove-mount clamp

A cardboard template should be made before cutting the aluminum.

Acrylic may be used for temporary geometry testing, but aluminum, polycarbonate, or FR4 is preferred for the permanent carriage.

## Bed

The CubeX bed consists of a glass print surface over an aluminum bed plate.

The printer currently has no active heated-bed system connected.

PLA will be used for initial testing because it is easier to print on an unheated glass bed than ABS or PETG.

Possible adhesion methods include:

- Glue stick
- Painter's tape
- PEI sheet
- Removable textured build surface

## Bed Probe

An LJ12A3-4-Z/BX inductive proximity sensor was purchased.

The probe is:

- NPN
- Normally open
- Designed for higher-voltage operation
- Intended to detect metal
- Approximately 4 mm nominal sensing distance

Because the CubeX bed has glass over aluminum, the probe may be able to detect the aluminum through the glass, depending on the glass thickness and probe position.

A 24 V to 5 V optocoupler isolation board will be used to prevent unsafe voltage from reaching the SKR input.

The original CubeX may also contain an existing leveling sensor. It has not yet been identified or tested for Marlin compatibility.

## Original Part-Cooling Fans

The CubeX carriage includes three original, well-positioned 12 V part-cooling fans.

The new printer power system is 24 V, so the original fans must not be connected directly to 24 V.

### Planned Reuse

The three fans can be connected in parallel to a 24 V to 12 V buck converter.

For speed control, the planned arrangement is:

```text
24 V PSU
    |
24 V to 12 V buck converter
    |
Three 12 V fans connected in parallel
    |
External MOSFET switching module
    |
SKR part-cooling fan control
```

The SKR fan output should control the MOSFET, while the fans receive regulated 12 V power.

## Original CubeX Screen

The original CubeX front screen is still available.

The goal is to control the converted printer from the front of the machine without requiring a computer.

The original display has not yet been connected to the SKR because its voltage, pinout, and communication protocol are unknown.

### Investigation Required

Before connecting the display:

- Identify the screen PCB
- Record all part numbers
- Identify the screen connector
- Count the connector pins
- Trace the original motherboard connection
- Determine the operating voltage
- Determine whether communication is serial, SPI, parallel, or proprietary
- Determine whether the display has its own processor
- Check whether it can communicate with Marlin

If the original screen cannot be reused, a BTT TFT display may be mounted behind the original CubeX front bezel.

Possible replacements include:

- BTT TFT35
- BTT TFT43
- BTT TFT50

## Useful Test Commands

### Read Hotend Temperature

```gcode
M105
```

### Heat Hotend Without Waiting

```gcode
M104 S200
```

### Heat Hotend and Wait

```gcode
M109 S200
```

### Relative Movement Mode

```gcode
G91
```

### Test Extruder

```gcode
G1 E5 F60
```

### Extrude 100 mm for E-Steps Calibration

```gcode
G1 E100 F100
```

### PID Tune Hotend

```gcode
M303 E0 S200 C8
```

### Display TMC Driver Status

```gcode
M122
```

### Save EEPROM Settings

```gcode
M500
```

### Load EEPROM Settings

```gcode
M501
```

### Display Current Settings

```gcode
M503
```

### Disable Cold Extrusion Protection Temporarily

Only use this with no filament loaded:

```gcode
M302 P1
```

Re-enable protection afterward:

```gcode
M302 P0
```

## Current Working Status

- [x] Original CubeX motherboard removed
- [x] SKR V1.4 Turbo installed
- [x] Marlin compiled
- [x] Firmware flashing works
- [x] USB communication works
- [x] TMC2209 UART communication works
- [x] X-axis movement works
- [x] Dual Y-axis movement works
- [x] Z-axis movement works
- [x] X endstop works
- [x] Y endstop works
- [x] X homing works
- [x] Y homing works
- [x] Hotend thermistor works
- [x] Heater cartridge works
- [x] Hotend PID tuning completed
- [x] Hotend fan works
- [x] Extruder motor works
- [x] Extruder direction corrected
- [x] Hotend and extruder temporarily mounted
- [ ] Permanent carriage completed
- [ ] Full bed travel restored
- [ ] E-steps calibrated
- [ ] Z endstop or probe installed
- [ ] Z homing configured
- [ ] Bed leveling configured
- [ ] Original part-cooling fans installed
- [ ] Original CubeX screen integrated
- [ ] Final print area configured
- [ ] First filament extrusion completed
- [ ] First calibration print completed

## Next Steps

1. Design and fabricate the permanent hotend carriage.
2. Restore access to the full print bed.
3. Confirm the final nozzle position.
4. Install the Bowden tube and 1.75 mm filament.
5. Calibrate E-steps.
6. Install the Z endstop or inductive probe.
7. Configure Z homing.
8. Configure automatic bed leveling.
9. Install the part-cooling system.
10. Configure final travel limits.
11. Set the Z offset.
12. Print a first-layer test.
13. Print a 20 mm calibration cube.
14. Tune flow, temperature, retraction, acceleration, and speed.
15. Investigate the original CubeX screen.

## Safety Notes

- Never work on wiring while the printer is powered.
- Never connect a component until its voltage and polarity are confirmed.
- Do not connect 12 V fans directly to 24 V.
- Do not connect the original CubeX screen to the SKR until its pinout is identified.
- Keep Marlin thermal runaway protection enabled.
- Do not heat the hotend if the thermistor reports an invalid temperature.
- Do not leave the printer unattended during initial heating or printing tests.
- Use an external MOSFET for high-current heated-bed loads.
- Add proper strain relief to all moving hotend and carriage wiring.
- Keep thermistor wires separated from heater and motor wiring where practical.
- Check all high-current screw terminals regularly.
