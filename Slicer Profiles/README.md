These profiles are not fully tested, but are a good starting point. The most important thing that makes these profiles valuable is the starting and ending scripts and machine definitions. The 'normal' quality settings in Simplify3D and Prusa slicer have been tested with PLA and work well.

One annoying limitation with all of them is that there isn't an easy way to tie material-dependent temperature to speed. The faster plastic is extruded, typically a higher temperature is used. For example, when printing a .12mm thick layer at 40mm/s (2400mm/min), 200°C will work just fine for PLA. However, when printing a .32mm thick layer at 60mm/s (3600mm/min) for the same material may require 215°C or higher. This can't be easily built into a small number of profiles, and therefore, the necessary adjustments are at the discretion of the operator.

Feel free to make your own profiles. Utilize the startup scripts in the README.md in the folders for each of the various slicers.
