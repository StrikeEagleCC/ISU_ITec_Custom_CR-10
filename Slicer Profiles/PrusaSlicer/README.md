# GCode Scripts for PrusaSlicer and Slic3r

**Startup Script**
```
; Startup script for ITec custom CR-10
M220 S100 ; set speed to 100%
M221 S100 ; set filament flow to 100%
M190 S[first_layer_bed_temperature] ; Wait for bed to preheat
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
M109 S[first_layer_temperature] T0; Now wait for extruder to finish preheat
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