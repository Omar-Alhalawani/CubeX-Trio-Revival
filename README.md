# CubeX Trio RepRap Conversion

## Overview

This project converts a discontinued **3D Systems CubeX Trio** into an open-source, Marlin-controlled 3D printer.

The original proprietary motherboard, filament system, and hotend are being replaced with modern RepRap-compatible components. The printer is initially being converted into a reliable **single-extruder Bowden printer**, with the option to add more extruders later.

## Main Goals

- Replace the proprietary CubeX electronics
- Run open-source Marlin firmware
- Use standard 1.75 mm filament
- Install a modern V6-style hotend
- Preserve as much of the original CubeX motion system and frame as possible
- Restore the original build volume
- Add automatic bed leveling
- Reuse original components where practical
- Eventually control the printer without requiring a connected computer

---

## Hardware

### Installed or purchased

- CubeX Trio frame and motion system
- BIGTREETECH SKR V1.4 Turbo
- LPC1769 processor
- 5 × TMC2209 V1.3 stepper drivers
- Redrex S-360-24 24 V power supply
- V6-style 24 V hotend
- 24 V heater cartridge
- 100K thermistor
- Dual-gear Bowden extruder
- Standard 1.75 mm PTFE tubing
- 24 V 3010 hotend heatsink fan
- 24 V 5015 part-cooling blower
- LJ12A3-4-Z/BX inductive proximity sensor
- 24 V to 5 V optocoupler isolation board
- External heated-bed MOSFET module
- 18 AWG silicone wire
- 22 to 26 AWG silicone wire
- Dupont connector and crimping kit

### Original CubeX parts being reused

- Aluminum frame
- Linear motion system
- X, Y, and Z stepper motors
- Dual Y-axis motors
- Glass print surface
- Aluminum bed plate
- Original endstop wiring where compatible
- Original aluminum carriage components
- Three original 12 V part-cooling fans
- Original front display, pending investigation

### Currently not installed

- Heated bed
- Inductive probe
- Original CubeX screen
- Original 12 V part-cooling fans
- Permanent custom hotend carriage

---

## Original Printer Disassembly

The following work was completed:

- Removed the original CubeX motherboard
- Preserved the original wiring connectors
- Removed the original proprietary hotend and extruder components
- Removed panels as needed to access the electronics and motion system
- Inspected the existing motors, fans, endstops, bed, and carriage hardware
- Retained the original CubeX aluminum carriage parts for possible modification

---

## Controller Installation

The CubeX motherboard was replaced with a:

```text
BIGTREETECH SKR V1.4 Turbo