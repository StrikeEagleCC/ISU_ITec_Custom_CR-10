# ISU_ITec_Custom_CR-10
Firmware, information, and slicer profiles for the ISU ITec club custom Creality CR-10's
https://github.com/StrikeEagleCC/ISU_ITec_Custom_CR-10

The two Creality CR-10s belonging to the ISU Itec Club Robotics Team have been modified in several notable ways that the user should be aware of.

To make additional changes to the firmware simple, I have included a portable installation of the Arduino IDE which has been preconfigured to work for Creality CR and Ender printers. To edit the firmware, download this repository and open "OpenFirmwareWindows.bat"

Special thanks to Michal from the YouTube channel [Teaching Tech](https://www.youtube.com/channel/UCbgBDBrwsikmtoLqtpc59Bw) and Chris from [TH3D](https://www.th3dstudio.com/) for their excellent guides and other resources.

## The most important things to know are:

1. **Add `G29` to your starting scripts!**
1. **Do not use metal scrapers on the ULTRABASE print surface! Allow it to cool completely and parts will pop off easily.** If parts are not coming off easily, the first layer may be getting too squashed.
1. **The bed size has been shrunk to 300mm x 255mm (X, Y)**
1. **The bed leveling knobs do not need to be adjusted (though they should be lightly snug)**
1. **The height of the nozzle above the bed can be adjusted from the menus:**
    1. _Control>Motion>Z Probe Offset_
    1. **Or** by double clicking the control knob during a print
1. **Each printer controller is paired with a particular printer frame (or specifically, with each hot end assembly) due to the**  **Z Probe Offset**  **setting. Don't mix them up.**

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
- No EEPROM chitchat
- No `M503`
- Printable volume is smaller
- No arc support
- No volumetrics


### New Features In Detail

#### Thermal Runaway Protection
Thermal runaway protection is an important safety feature that was not included in the original printer firmware. This feature prevents the bed or extruder from overheating in the event that the thermister fails. This failure has been the cause of 3D printers in the past, and is now widely considered to be a critical safety feature. This is just a software feature, and required no hardware modifications. This feature also requires no interaction with the user. [Read more here.](http://marlinfw.org/docs/configuration/configuration.html#safety)

#### BLTouch Probe
The BLTouch probe is the device on the hotend carriage with the red light in it. This device uses a small solenoid, a pin, and a hall effect sensor to measure the position of the print bed relative to the nozzle. In the simplest implementation, it just replaces the Z-axis limit switch, but its true value is that it facilitates mesh bed leveling.

Using a Z-axis probe creates an additional and very important setting, _Probe Z Offset_. This setting represents the distance between the probe's measurement point and the bottom of the nozzle. It can be adjusted from the menus _Control>Motion>Probe Z Offset_ or by double-clicking the control wheel during a print. The second method is extremely handy to make fine adjustments to the nozzle height during the first layer.

Under some circumstances, the probe may encounter an error and begin flashing. This error can be cleared by either power cycling the printer or navigating to the BLTouch sub menu and resetting it: _Control>BL Touch>Reset BL Touch_

Installing the probe requires the use of a pin on the printer's microprocessor which was previously used to sound the buzzer. To make the use of the probe possible, the buzzer has been disabled, and the pin repurposed for use with the probe.Read more about [probes in Marlin](http://marlinfw.org/docs/configuration/configuration.html#z-probe-options) and more about the [BLTouch](https://www.antclabs.com/bltouch).

#### Automatic Mesh Bed Leveling
Auto bed leveling, is a process that makes it possible to get very reliable first layers, even on beds that aren't perfectly flat or level. This is done by taking several z-axis measurements of the print bed when starting a print. The printer then uses these measurements to build a map of the print bed, then automatically adjusts the nozzle height  during printing to conform to the shape of the bed. This effect is gradually faded out so that after a short vertical distance it is no longer in effect.

Mesh bed leveling compensates both for beds that aren't flat and beds that aren't level. This means that the bed doesnt have to be perfectly level, and so the bed adjustment springs have been replaced with solid nylon spacers. As long as the leveling knobs aren't loose, no adjustment is needed. Bed leveling does not correct an out of square condition between the Z-axis and the print bed plane. Very large parts with perpendicular surfaces and close tolerances may require more careful shimming of the nylon spacers to bring the print bed into square with the Z-axis, but this is extremely unlikely.

Mesh bed leveling should be performed at the beginning of every print. This is done by adding a G29 command in the starting script in the slicer. Failure to add this will almost certainly lead to first layer failure, will likely lead to a crash, and may lead to nozzle or bed damage.

Mesh bed leveling is done in software, but requires the use of a probe such as the BLTouch. You can [read more about it here](http://marlinfw.org/docs/configuration/configuration.html#bed-leveling).

#### Linear Pressure Control
Also called Linear Advance, this feature aims to improve print quality by controlling the pressure in the nozzle. This is done by adding compression to the filament in an amount proportional to the extrusion velocity and a constant configurable in the machine settings. There is a lot of interesting [information available about it here](http://marlinfw.org/docs/features/lin_advance.html), but here's what you need to know:

Linear advance causes a lot of extruder motor movement. If you're familiar with FDM machines, the movement of the extruders on these modified machines may surprise you. The setting to control the amount of linear advance is in _Control>Filament>Advance K_. A setting of 0.6 is best for this printer. Changing this setting to zero will disable the feature. If disabled, you may need to increase retraction distances in the slicer to avoid stringing, and you can expect to see less detail and more blobs in corners.

This feature is entirely software based and required no hardware modifications.

#### Filament Runout Detection
The purpose of filament runout detection is to stop a print in the event that your filament spool runs empty. This has been implemented on these printers with a custom filament sensor mounted on the extruder motor frame. Filament is fed through the sensor module and into the extruder. If the filament runs out, the sensor module will send a signal to the printer, which will stop the print, move the print head to a parkin position, unload the filament and prompt the operator for action.

The park, unload, and prompt actions are part of a filament change sequence that can be manually commanded with a `M600` command. In fact, when the filament sensor detects a runout condition, the machine just gives itself an `M600`. You can execute this command deliberately from a console, or better yet, insert it into your gcode at strategic places to facilitate the use of multiple filament colors in a single print

If the filament is very curly when feeding it through the sensor, it can become snagged. Straighten the filament prior to threading it through. If the sensor becomes damaged or unwanted, it's function can be bypassed by shorting pins 1 and 3 (signal and ground, orange and brown) of the wiring harness plug.

I have also had the filament jam in the sensor after a runout condition. If this happens, cut the filament between the sensor and the extruder and pull the piece from the sensor out the extruder side. Heat up the extruder and then pull the remaining filament out from the extruder. Install the new filament as usual.

The filament sensor required the use of a pin on the microprocessor that was previously unused. A very small wire was soldered to this pin and used as the signal pin for the filament sensor. The M600 command is part of another feature called _Advanced Pause._ You can read more about filament runout sensors [here](http://marlinfw.org/docs/configuration/configuration.html#filament-runout-sensor) and about the advanced pause feature [here](http://marlinfw.org/docs/configuration/configuration.html#advanced-pause)

#### Persistent Settings
The original firmware on these printers did not support persistent settings. You could make changes to the settings form the menus or by issuing applicable commands, but if the printer was powered cycles, those settings would be replaced by the default values.

To store settings such as _Probe Z Offset_ or _Advanced K_, navigate to _Control>Store Settings_. Those settings will now persist through a reboot. To revert to the default settings, select _Restore failsafe_. **Note** : **this will reset your _Probe Z-Offset_ to zero, and you will have to reset it!**

### Lost Features In Detail

#### Buzzer No Longer Functional
The buzzer was disconnected so that its control pin could be used for the Z probe

#### Print Volume Is Smaller
The Y-axis travel has been reduced in firmware, and the Y-axis end stop has been moved to a position closer to the front of the printer. Together, these two changes prevent the nozzle and the fan ducts from hitting the clips that hold the glass plate to the heated bed. The reduced size in one axis is extremely unlikely to cause a problem printing a single part, as parts that large are unusual. However, the reduced bed size does limit the ability to print many smaller objects in a single job. Still, 300mm x 255 mm is a very large print bed, and I've never wanted more than that.

One practical effect of this change is that default slicer profiles will have incorrect y-axis dimensions. If you use profile other than the ones I've created, double check your settings to ensure you don't attempt to print outside the build envelope. (Also, make sure you add `G29` to the start script.)

The actual print volume of these modified printers is 300mm x 255mm x 400mm (x, y z)

#### Some Menu Items No Longer Visible
Part of keeping the price of these printers low meant using inexpensive electronics. The microcontroller for the CR-10 (and most Creality printers) does not have enough memory to store a full featured firmware. In order to make space for the new features, some other features were disabled. Most noticeable is that several menu items are gone.

The most noticeable menu items missing are feedrate, acceleration and jerk settings in the motion menu. Other settings no longer accessible through the menu include motor steps per mm, PID settings, material preset temps, and probably some others I haven't noticed.

These settings can still be changed with the appropriate g-code commands, and then saved to the EEPROM by sending `M500` or by navigating to _Control>Store settings_, you just won't be able to change the settings from the menu.

#### No EEPROM Chitchat
EEPROM Chitchat is a feature that provides feedback through a terminal about EEPROM read/write status. It's handy for a few things, but not necessary for a typical user. It was disabled to save space.

#### No `M503` Support
When `M503` is sent to the printer, the printer responds by listing the current value of every configurable setting. This is extremely handy for getting snapshot of your printer's current configuration for future reference. However, it had to be disabled to save space. Read more [here](https://github.com/MarlinFirmware/Marlin/wiki/M503).

#### No Arc Support
Barely worth mentioning, since most slicers don't output arc commands by default. Disabled to save space.

#### No Volumetrics
Volumetric extrusion is a feature that allows gcode for a given print to exclude information about the filament diameter and temperatures--these are then selected at print time. If you need it, then you probably know what it is. Disabled to save space.

### Printer Default Values

These are the default values that will be loaded if you click _Control>Restore failsafe_. Note that these settings will not persist through a reboot unless you store them with _Control>Store Settings_.

    G21    ; (mm)
    M149 C ; Units in Celsius
    Steps per unit:
    M92 X80.00 Y80.00 Z400.00 E93.50
    Maximum feedrates (units/s):
    M203 X500.00 Y500.00 Z20.00 E40.00
    Maximum Acceleration (units/s2):
    M201 X1000 Y1000 Z100 E5000
    Acceleration (units/s2): P<print_accel> R<retract_accel> T<travel_accel>
    M204 P500.00 R1000.00 T1000.00
    Advanced: Q<min_segment_time_us> S<min_feedrate> T<min_travel_feedrate> X<max_x_jerk> Y<max_y_jerk> Z<max_z_jerk> E<max_e_jerk>
    M205 Q20000 S0.00 T0.00 X10.00 Y10.00 Z0.40 E13.00
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
    M900 K0.60
    Filament load/unload lengths:
    M603 L0.00 U500.00
