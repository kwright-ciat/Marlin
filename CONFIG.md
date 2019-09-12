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
M117 Heating
M140 S{material_bed_temperature_layer_0}
M104 S{material_print_temperature_layer_0}
M109 S{material_print_temperature_layer_0}
M190 S{material_bed_temperature_layer_0}
M117 Done heating

M569 S0 E ; Disable stealthchop for extruder

M851 Z{material_z_offset} ; Set z probe offset, -0.66
M900 K{material_linear_advance_factor} ; Set linear advance K value, 0.07
M205 J0.06 ; Set junction deviation

M117 Homing
G28 ; Home all axes
M117 Probing
G29 ; Auto bed-level (BL-Touch)

M117 Purging
G90 ; Absolute positioning
G92 E0 ; Reset Extruder
G1 Z10.0 F3000 ; move z up little to prevent scratching of surface
G1 X0.1 Y20 Z0.3 F5000.0 ; move to start-line position
G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; draw 1st line
G1 X0.4 Y200.0 Z0.3 F5000.0 ; move to side a little
G1 X0.4 Y20 Z0.3 F1500.0 E30 ; draw 2nd line
G92 E0 ; reset extruder
G1 Z1.0 F3000 ; move z up little to prevent scratching of surface

M117 Printing
```

## End GCode

```
G91 ;Relative positionning
G1 E-2 F2700 ;Retract a bit
G1 E-2 Z0.2 F2400 ;Retract and raise Z
G1 X5 Y5 F3000 ;Wipe out
G1 Z10 ;Raise Z more
G90 ;Absolute positionning

G1 X0 Y{machine_depth} ;Present print
M106 S0 ;Turn-off fan
M104 S0 ;Turn-off hotend
M140 S0 ;Turn-off bed

M84 X Y E ;Disable all steppers but Z
```