These profiles are not fully tested, but are a good starting point. The most important thing that makes these profiles valuable is the starting and ending scripts and machine definitions.

One annoying limitation with all of them is that there isn't an easy way to tie material-dependent temperature to speed. The faster plastic is extruded, typically a higher temperature is used. For example, when printing a .12mm thick layer at 40mm/s (2400mm/min), 200°C will work just fine for PLA. However, when printing a .32mm thick layer at 60mm/s (3600mm/min) for the same material may require 215°C or higher. This can't be easily built into a small number of profiles, and therefore, the necessary adjustments are at the discretion of the operator.

Feel free to make your own profiles and to utilize these start and end scripts:
**Startup Script**
```
; Startup script for ITec custom CR-10
M220 S100 ; set speed to 100%
M221 S100 ; set filament flow to 100%
M190 S[bed0_temperature] ; Wait for bed to preheat
; Allow several minutes for bed temperature to stabilize.
G4 P60000 ; Wait additional 60 seconds for bed temp to stabilize
G4 P60000 ; Wait additional 60 seconds for bed temp to stabilize
G4 P60000 ; Wait additional 60 seconds for bed temp to stabilize
G4 P60000 ; Wait additional 60 seconds for bed temp to stabilize
G4 P60000 ; Wait additional 60 seconds for bed temp to stabilize
G90 ; Absolute measurement
G28 ; Home all axes
G29 ; Bed leveling
G1 X1 Y29 F18000 ; Move to prime
M109 S[extruder0_temperature] T0; Now wait for extruder to finish preheat
G1 Z0.3 F1000 ; get ready to prime
G92 E0 ; Reset extrusion distance
G1 Y138 E12 F600 ; prime nozzle
G92 E0 ; Reset extrusion distance
```

**Ending Script**
```
; Ending script for Itec custom CR-10
G92 E0 ; Reset extrusion distance
G1 E-3.00 F2400 ; Retract
G91 ; incremental measurement
G1 Z1 F1000 ; z-lift from print
G90 ; absolute measurement
G28 X0 ; home x axis
M106 S0 ; turn off cooling fan
M104 S0 ; turn off extruder
M140 S0 ; turn off bed
M84 ; disable motors
```

**Printer Dimensions and Max Limits**
Parameter|Value
Bed X Width|300mm
Bed Y Depth|300mm
Z Height|400mm
Build Plate Shape|Rectangular
Origin|Front Left
Heated Bed|Yes
GCode Flavor|Marlin
X-min|0
