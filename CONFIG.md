# Tom's Ender 3 configuration

## GCode command notes

1. M851 Z <z-offset> sets Z offset
2. G28 <axis> homes axis, i.e. G28 Z
3. G1 F60 <axis><position> i.e. G1 F60 Z0 moves to 0 on Z
4. M211 S0 disable soft limit switches (S1 to re-enable)
5. G29 to do mesh bed level with probe

BLTouch setup

1. M851 Z0 to clear any Z offset
2. G28 Z to home Z axis
3. M211 S0 to allow soft limits to move below 0
4. Lower Z until at zero, or approximate using paper. Check z offset
5. M851 Z <offset> where <offset> is the (probably negative) z value from (4)
6. M211 S1 to enable soft limits again

## Start GCode

```
; Ender 3 Custom Start G-code
;G28 ; Home all axes
;G92 E0 ; Reset Extruder
;G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
;G1 X0.1 Y20 Z0.3 F5000.0 ; Move to start position
;G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
;G1 X0.4 Y200.0 Z0.3 F5000.0 ; Move to side a little
;G1 X0.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line;
;G92 E0 ; Reset Extruder
;G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
; End of custom start GCode

; Ender 3 Custom Start G-code
M117 Heating bed
M140 S{material_bed_temperature_layer_0} ; Set Heat Bed temperature
M190 S{material_bed_temperature_layer_0} ; Wait for Heat Bed temperature
M117 Warming extruder to 160
M104 S160; start warming extruder to 160
G28 ; Home all axes
M117 BLTouch probing
G29 ; Auto bed-level (BL-Touch)
G92 E0 ; Reset Extruder
M117 Heating extruder
M104 S{material_print_temperature_layer_0} ; Set Extruder temperature
M109 S{material_print_temperature_layer_0} ; Wait for Extruder temperature
G1 Z10.0 F3000 ; move z up little to prevent scratching of surface
G1 X0.1 Y20 Z0.3 F5000.0 ; move to start-line position
M117 LET THE PURGE BEGIN!
G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; draw 1st line
G1 X0.4 Y200.0 Z0.3 F5000.0 ; move to side a little
G1 X0.4 Y20 Z0.3 F1500.0 E30 ; draw 2nd line
G92 E0 ; reset extruder
G1 Z1.0 F3000 ; move z up little to prevent scratching of surface
M117 Preparation done
; End of custom start GCode
```

## End GCode

```
; Ender 3 Custom End G-code
G4 ; Wait
M220 S100 ; Reset Speed factor override percentage to default (100%)
M221 S100 ; Reset Extrude factor override percentage to default (100%)
G91 ; Set coordinates to relative
G1 F1800 E-3 ; Retract filament 3 mm to prevent oozing
G1 F3000 Z20 ; Move Z Axis up 20 mm to allow filament ooze freely
G90 ; Set coordinates to absolute
G1 X0 Y{machine_depth} F1000 ; Move Heat Bed to the front for easy print removal
M84 ; Disable stepper motors
; End of custom end GCode
```