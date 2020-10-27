# GCode Scripts for Cura

Cura doesn't appear to support exporting printer configurations. To make a printer profile for Cura, you will need the following information to add a custom FFF printer:

Setting Name|Value
---|---
X(Width)|300
Y(Depth)|300
Z(Height)|400
Build plate shape|Rectangular
Origin at center|no
Heated bed|yes
Heated build volume|no
G-code flavor|Marlin
X min|-60
Y min|-22
X max|38
Y max|40
Gantry height|25
Number of Extruders|1
Start G-code|See scripts below
End G-Code|See scripts below
Nozzle size|0.4
Compatible material diameter|1.75
Nozzle offset X|0
Nozzle offset Y|0
Cooling Fan Number|0
Extruder Start G-code|None
Extruder End G-code|None

**Here's a problem with Cura**. . . I think.

I haven't figured out an easy way to use variables in the g-code scripts, so unlike other slicers, if you slice with cura and want the following scripts to work, you have to manually edit the gcode file after saving it. First, delete all the lines that appear before the startup script. Then, replacie 	`[first_layer_bed_temp]` with the temperature you want the bed to be for the first layer and `[first_layer_extruder_temp]` with the temperature you want the extruder to be for the first layer. For example, if you want your bed at 60 degrees and your extruder at 210 degrees:

`M190 S[first_layer_bed_temp]`

`M109 S[first_layer_extruder_temp]`

become:

`M190 S60`

`M109 S210`

**Start G-code**
```
; Startup script for ITec custom CR-10
M220 S100 ; set speed to 100%
M221 S100 ; set filament flow to 100%
M190 S[first_layer_bed_temp] ; Wait for bed to preheat
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
M109 S[first_layer_extruder_temp] T0; Now wait for extruder to finish preheat
G1 Z0.3 F1000 ; get ready to prime
G92 E0 ; Reset extrusion distance
G1 Y138 E12 F600 ; prime nozzle
G92 E0 ; Reset extrusion distance
```

**End G-code**
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
