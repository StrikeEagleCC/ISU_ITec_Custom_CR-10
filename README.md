# ISU_ITec_Custom_CR-10
Firmware, information, and slicer profiles for the ISU ITec club custom Creality CR-10's
https://github.com/StrikeEagleCC/ISU_ITec_Custom_CR-10

## 2020 UPDATE
- moved to flexible, removable magnetic print surface
- Restored full build volume through better implementation of homing and mesh probing
- Reverted back to bed springs instead of nylon spacers to reduce severity of crashes, and used better-than-stock springs
- [New and Improved slicer profiles!](https://github.com/StrikeEagleCC/ISU_ITec_Custom_CR-10/tree/master/Slicer%20Profiles)
- Removed Slic3r profiles from this repository
- Added PrusaSlicer profiles to this repository
- **All slicer profile updates are still in work at this time**

To make additional changes to the firmware simple, I have included in this repository a portable installation of the Arduino IDE which has been preconfigured to work for Creality CR and Ender series printers. To edit the firmware, download this repository and open "OpenFirmwareWindows.bat"

Special thanks to Michael from the YouTube channel [Teaching Tech](https://www.youtube.com/channel/UCbgBDBrwsikmtoLqtpc59Bw) and Chris from [TH3D](https://www.th3dstudio.com/) for their excellent guides and other resources. And of course, to the Marlin developer community for all of their excellent work.

---

The two Creality CR-10s belonging to the ISU Itec Club Robotics Team have been modified in several notable ways that the user should be aware of.


## The most important things to know are:

1. **Add `G29` to your starting scripts!**
1. **The flexible magnetic print surfaces work well for PLA and PETG. However, the ULTRABASE glass surface can still be used if desired. Simply remove the magnetic sheet and replace with the glass.**
1. **When placing the manetic print surface on the bed, ensure there is no debris between the print surface and the bed. This is especially important if the printers continue to be stored in the CNC lab, as metal chips will be attracted to the magnetic surfaces.**
1. **Do not use metal scrapers on the ULTRABASE glass print surface! Allow it to cool completely and parts will pop off easily.** If parts are not coming off easily, the first layer may be getting too squashed.
1. **If using the glas print surface, don't attempt to print close to the front or back edge. The print head will hit the clips that hold the glass to the bed.**
1. **The shape of the bed will continue to change for several minutes after preheating has completed. Allow several minutes at full temperature before starting the bed-leveling probing.** This can be done by adding delays to starting scripts. Read more below.
1. **The bed leveling knobs do not need to be adjusted. They have been carefully set and locked with jam nuts. Any remaining out-of-level condition should be compensated for by the BLTouch probe and software bed leveling**
1. **The height of the nozzle above the bed can be adjusted from the menus to dial in the height of the first layer in real time:**
    1. _Control>Motion>Probe Z Offset_
    1. **Or** by double clicking the control knob during a print (making live adjustments this way is called "baby-stepping")
1. **Each printer controller is paired with a particular printer frame due to the** **_Probe Z Offset_**  **setting being unique to each printer. Don't mix them up.**

The modifications on these machines were made for the sake of adding several desirable features for both safety and functionality. However, given the low price point of the printers, some trade-offs had to be made to accommodate the new features. The new features and the features lost in exchange are listed below, and then discussed in further detail. The changes involved the installation of some new hardware, the modification of the original hardware, and the installation of a customized version of the Marlin firmware

**New Features**

- Thermal runaway protection for both bed and extruder
- BLtouch probe
- Automatic Mesh bed leveling
- Linear pressure control
- Filament runout detection
- Persistent settings

**Lost Features**

- Buzzer no longer functional
- Some menu items no longer visible
  - Any functions for which menu items were removed are still accessible through Gcode, either through GCode files or through a serial console like pronterface or Simplify3D's machine control panel.
- No arc support
- No volumetrics
- No M114

---

### New Features In Detail

#### Thermal Runaway Protection
Thermal runaway protection is an important safety feature that was not included in the original printer firmware. This feature prevents the bed or extruder from overheating in the event that the thermister fails. This failure has been the cause of 3D printer fires in the past, and is now widely considered to be a critical safety feature. This is just a software feature, and required no hardware modifications. This feature also requires no interaction with the user. [Read more here.](http://marlinfw.org/docs/configuration/configuration.html#safety)

#### BLTouch Probe
The BLTouch probe is the device on the hotend carriage with the red light in it. This device uses a small solenoid, a pin, and a hall effect sensor to measure the position of the print bed relative to the nozzle. In the simplest implementation, it just replaces the Z-axis limit switch, but its true value is that it facilitates mesh bed leveling.

Using a Z-axis probe creates an additional and very important setting, _Probe Z Offset_. This setting represents the distance between the probe's measurement point and the bottom of the nozzle. It can be adjusted from the menus _Control>Motion>Probe Z Offset_ or by double-clicking the control wheel during a print. The second method is extremely handy to make fine adjustments to the nozzle height during the first layer.

Under some circumstances, the probe may encounter an error and begin flashing. This error can be cleared by either power cycling the printer or navigating to the BLTouch sub menu and resetting it: _Control>BL Touch>Reset BL Touch_

Installing the probe requires the use of a pin on the printer's microprocessor which was previously used to sound the buzzer. To make the use of the probe possible, the buzzer has been disabled, and the pin repurposed for use with the probe.Read more about [probes in Marlin](http://marlinfw.org/docs/configuration/configuration.html#z-probe-options) and more about the [BLTouch](https://www.antclabs.com/bltouch).

#### Automatic Mesh Bed Leveling
Auto bed leveling is a process that makes it possible to get reliable first layers, even on beds that aren't perfectly flat or level. This is done by taking several z-axis measurements of the print bed when starting a print. The printer then uses these measurements to build a map of the print bed, then automatically adjusts the nozzle height  during printing to conform to the shape of the bed. This effect can be gradually faded out so that after a short vertical distance it is no longer in effect.

Mesh bed leveling compensates both for beds that aren't flat and beds that aren't level. This means that the bed doesnt have to be perfectly level. As long as the leveling knobs aren't loose, no adjustment is needed.

**An important limitation** of thes printers and mesh bed leveling performed on them is the cantilevered design of the X-axis gantry. Because it is raised in the Z-axis only on the left side, there is some movement hysteresis (kind of like backlash) on the right side. Since the probing is done with the gantry moving downwards, and the prints are done with the gantry moving upwards, this hysteresis can have an effect. In practice, I find that even after an automatic bed leveling, my first layers are a bit thinner on the right side of the bed as on the left. Some careful adjustment of the X-axis gantry rollers has improved this somewhat. Still, it's easier than manual leveling all the time, and still helps to compensate for non-flat bed surfaces. **Don't attempt first layers thinner than 0.2mm.** The thicker the first layer is, the easier it is to put down.

Mesh bed leveling should be performed at the beginning of every print. This is done by adding a `G29` command in the starting script in the slicer. Failure to add this will almost certainly lead to first layer failure, may lead to a crash and to nozzle or bed damage. Also, as noted previously, the bed will continue to change shape for several minutes after preheating has completed. To allow for this, add a delay before `G29` in the starting script with `G4 P<time in milliseconds>`. Delays greater than 60 seconds may cause problems in some scenarios, so for a 5 minute delay, add `G29 P60000` five times in the starting script.

Mesh bed leveling is done in software, but requires the use of a probe such as the BLTouch. You can [read more about it here](http://marlinfw.org/docs/configuration/configuration.html#bed-leveling).

#### Linear Pressure Control
Also called Linear Advance, this feature aims to improve print quality by controlling the pressure in the nozzle. This is done by adding compression to the filament in an amount proportional to the extrusion velocity and a controlled by a constant factor configurable in the machine settings. There is a lot of interesting [information available about it here](http://marlinfw.org/docs/features/lin_advance.html), but here's what you need to know:

Linear advance causes a lot of extruder motor movement. If you're familiar with FDM machines, the movement of the extruders on these modified machines may surprise you. The setting to control the amount of linear advance is in _Control>Filament>Advance K_, or by issuing a `M900<factor>`. A factor of 0.6 works well for PLA on these printers. Changing this setting to zero will disable the feature. If disabled, you may need to increase retraction distances in the slicer to avoid stringing, and you can expect to see less detail and more blobs in corners than if the feature is enabled.

This feature is entirely software based and required no hardware modifications.

#### Filament Runout Detection
The purpose of filament runout detection is to stop a print in the event that your filament spool runs empty. This has been implemented on these printers with a custom filament sensor mounted on the extruder motor frame. Filament is fed through the sensor module and into the extruder. If the filament runs out, the sensor module will send a signal to the printer, which will stop the print, move the print head to a parking position, unload the filament and prompt the operator for action.

The park, unload, and prompt actions are part of a filament change sequence that can be manually commanded with a `M600` command. In fact, when the filament sensor detects a runout condition, the machine just gives itself an `M600`. You can execute this command deliberately from a console, or better yet, insert it into your gcode at strategic places to facilitate the use of multiple filament colors in a single print.

The current runout sensor that I designed is not very good. When the filament runs out, it usually jams in the sensor housing when the printer tries to automatically unload it. If this happens, cut the filament between the sensor and the extruder and pull the piece from the sensor out the extruder side. Heat up the extruder and then pull the remaining filament out from the extruder. Install the new filament as usual.

Also, if the filament is very curly when feeding it into the sensor, it can become snagged. Straighten the filament prior to threading it through.

If the sensor becomes damaged or unwanted, just unplug it. If you want to use a different sensor be aware that a firmware modification (and possibley a hardware modification) is likely to be necessary.

The filament sensor required the use of a pin on the microprocessor that was previously unused. A very small wire was soldered to this pin and used as the signal pin for the filament sensor. The M600 command is part of another feature called _Advanced Pause._ You can read more about filament runout sensors [here](http://marlinfw.org/docs/configuration/configuration.html#filament-runout-sensor) and about the advanced pause feature [here](http://marlinfw.org/docs/configuration/configuration.html#advanced-pause)

#### Persistent Settings
The original firmware on these printers did not support persistent settings. You could make changes to the settings form the menus or by issuing applicable commands, but if the printer was powered cycled, those settings would be replaced by the default values.

To store settings such as _Probe Z Offset_ or _Advanced K_, navigate to _Control>Store Settings_, or issue an `M500` command from a console. Those settings will now persist through a reboot. To revert to the default settings, select _Restore failsafe_ or issue an `M502` command from a console. **Note** : **this will reset your _Probe Z-Offset_ to zero, and you will have to reset it!** Restoring default settings will not overwrite the stored settings unless you follow `M502` with `M500`

If you make changes to settings and want to revert them to the stored settings, just power cycle the printer or issue an `M501` command to restore the stored settings.

---

### Lost Features In Detail

#### Buzzer No Longer Functional
The buzzer was disconnected so that its control pin could be used for the Z probe

#### Some Menu Items No Longer Visible
Part of keeping the price of these printers low meant using inexpensive electronics. The microcontroller for the CR-10 (and most Creality printers) does not have enough memory to store a fully featured firmware. In order to make space for the new features, some other features were disabled. Most prominently, several menu items are gone.

The most noticeable menu items missing are feedrate, acceleration and jerk settings in the motion menu. Other settings no longer accessible through the menu include motor steps per mm, PID settings, material preset temps, and probably some others I haven't noticed.

These settings can still be changed with the appropriate g-code commands, and then saved to the EEPROM by sending `M500` or by navigating to _Control>Store settings_, you just won't be able to change the settings from the menu.

#### No Arc Support
Barely worth mentioning, since most slicers don't output arc commands by default. Disabled to save space.

#### No Volumetrics
Volumetric extrusion is a feature that allows gcode for a given print to specify the volume of plastic to be extruded through the nozzle, as opposed to the length of filament driven by the extruder. This allows the same g-code file to be used on multiple printers using different diameter filaments. If you need it, then you probably know what it is, but it's rarely used. Disabled to save space.

### Printer Default Values

These are the default values that will be loaded if you click _Control>Restore failsafe_. Note that these settings will not persist through a reboot unless you store them with _Control>Store Settings_.

```
G21    ; (mm)
M149 C ; Units in Celsius
Steps per unit:
M92 X80.00 Y80.00 Z400.00 E93.50
Maximum feedrates (units/s):
M203 X500.00 Y500.00 Z20.00 E100.00
Maximum Acceleration (units/s2):
M201 X1000 Y1000 Z100 E5000
Acceleration (units/s2): P<print_accel> R<retract_accel> T<travel_accel>
M204 P600.00 R1000.00 T1000.00
Advanced: Q<min_segment_time_us> S<min_feedrate> T<min_travel_feedrate> X<max_x_jerk> Y<max_y_jerk> Z<max_z_jerk> E<max_e_jerk>
M205 Q20000 S0.00 T0.00 X5.00 Y5.00 Z0.40 E15.00
Home offset:
M206 X0.00 Y0.00 Z0.00
Auto Bed Leveling:
M420 S0 Z0.00
Material heatup parameters:
M145 S0 H180 B60 F127
M145 S1 H230 B100 F127
PID settings:
M301 P21.04 I1.59 D69.79
M304 P482.57 I95.00 D612.80
Z-Probe Offset (mm):
M851 Z0.00
Linear Advance:
M900 K0.65
Filament load/unload lengths:
M603 L0.00 U500.00
```

---

## Other Useful Information

###  New Plug Pinouts

These pictures are **of the plugs on the control box wiring harnesses**, not the plugs from the devices.

#### BLTouch Plug
![](https://drive.google.com/uc?export=view&id=1urE4j554T_cwNwk07C6WoUY2Gdjhd7SA "BLTouch Plug Pins")

Pin | Function
:---: |---
1 | No contact
2|Z-axis endstop signal
3|Ground
4|BLTouch servo signal
5|+5V
6|Ground

Pin 2 connects to the same port on the board as the Z-axis endstop switch used to. The BLTouch uses it to signal when the probe pin has been depressed. Pin 4 is connected to pin 27 on the motherboard, and is used to tell the BLTouch to deploy or retract the probing pin.

#### Filament Runout Plug
![](https://drive.google.com/uc?export=view&id=1BZnMTavnZmJFPT1uhG6D9j0MvsMsLLij "Filament Runout Pins")

Pin | Function
:---:|---
1|Ground
2|+5V
3|Signal

Pin 3 is connected to pin 29 on the motherboard. It is used to sense the presence of filament. The firmware interprets the state of the signal pin as follows:

Pin State|Runout State
---|---
High (floating, has internal pullup)|Filament present
Low (grounded)|Filament out

If a different runout sensor is purchased or made that uses an inverted scheme, the logic can be inverted in the firmware.


#### Other Non-Stock Parts
Part|Detail|Source
---|---|---
Flexible Magnetic Print Surface|Creality|[amazon.com](https://www.amazon.com/gp/product/B07GZ7QQTS/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
Black Glass Bed|Anycubic Ultrabase|[Anycubic3D.com](https://www.anycubic.com/collections/heated-bed-glass-plate/products/platform-tempered-glass-plate-310mmx310mm-for-large-print-size)
Bowden tube|Capricorn XS|[captubes.com](https://www.captubes.com/shop/#!/2-Meters-XS-Low-Friction-1-75mm-Bowden-Tubing/p/82434216/category=23214267)
Tube fittings|Capricorn|See above
Bed Springs|FYSETC|[amazon.com](https://www.amazon.com/gp/product/B07GXC1G2B/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
Silicone Sock|Creality|[amazon.com](https://www.amazon.com/gp/product/B07HP2SWJX/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
Filament Runout Sensor|Custom Design
Fan Duct|Petsfang Bullseye|[thingiverse.com](https://www.thingiverse.com/thing:2759439)
Bed Leveling Knobs||Unknown
Bed Cable Strain Relief||Unknown

### Potential Replacement Parts

Part Name|Replacement Mfg|Mfg P/N|Source
---|---|---|---
Hotend Fan*|Delta Electronics|AFB0412VHA-A|[digikey.com](https://www.digikey.com/en/products/detail/delta-electronics/AFB0412VHA-A/2743418)
Part Cooling Fan*|Delta Electronics|BFB0412HHA-A|[digikey.com](https://www.digikey.com/product-detail/en/delta-electronics/BFB0412HHA-A/603-1363-ND/2560487)
Power Supply*|MEAN WELL|LRS-12-350|[digikey.com](https://www.digikey.com/product-detail/en/mean-well-usa-inc/LRS-350-12/1866-3344-ND/7705030)
Board Fan*|Sunun Fans|	MF50151V1-1000U-A99|[digikey.com](https://www.digikey.com/product-detail/en/sunon-fans/MF50151V1-1000U-A99/259-1817-ND/7652225)
Enclosure Fan|Delta Electronics|EFB0412MD|[digikey.com](https://www.digikey.com/product-detail/en/delta-electronics/EFB0412MD/603-1853-ND/2328336)

**Notes:**
 
 *The board fans have already been replaced in both printers
 *The part cooling fans have already been replaced in both printers
 *The hotend fans have already been replaced on both printers
 *The power supply has already been replaced in one printer
 
 If the hotend fan needs replacement, don't try to get a super high flow upgrade. Too much cooling can be a problem.  Regardless of which fan is used to replace it, printing temperatures may need adjustment.
 
 The part specified for the cooling fan *should* have more flow and higher pressure than the stock fan. Fan speeds and/or print temperatures may require adjustment after replacement. Generally, PLA does well with lots of cooling though. Get the most powerful fan that will fit.
 
 The power supplies supplied with these printers are garbage. While they do function, they make clicking and squealing noises, and the fans can vibrate like crazy. I found loose balls of solder in one of them. I've opened both and don't think they pose a serious safety risk now that the loose solder has been removed. However, I don't have faith in their longevity. Edit: One has failed already and has been replaced.
