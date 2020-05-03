# Auto Bed Levelling on Anet A8 Plus and Marlin 2.0 Firmware
A step by step guide to install Marlin 2.0 on your Anet A8 Plus and set an inductive bed sensor.

After having used my Anet A8 for a few years, I've decided to step up and get the newer (upgraded) version, the Anet A8 Plus. More than just having a larger printing area (300 x 300 x 350mm), the Anet A8 Plus also features integrated mosfets to protect over charging the motherboard, has an automatic filament loading function, a new (loud) PSU and many more new features that makes it easy (well… in theory) to use for beginners in 3D printing.

As soon as I started using the Anet A8 Plus, I quickly realised that the manual calibration of the bed would become an issue for me. Indeed, due to the bigger surface, the bed calibration needs to be done perfectly in order to obtain decent print quality.
In the past, with my old Anet A8, I have used the Marlin firmware (1.9) combined with a probe to sense the bed and auto-level it.
Here I am showing the steps required to produce the same results with the Anet A8 Plus and the inductive sensor LJ18A3–8-Z/BX (available on Banggood https://www.banggood.com/8mm-LJ18A3-8-ZBX-Autolevel-Inductive-Sensor-For-Anet-A8-A2-A6-3D-Printer-p-1198244.html). I mounted the sensor with: https://www.thingiverse.com/thing:3811227 .
This guide is partially based on Tom Hofman's really good tutorial (https://tomhofman.nl/anet-a8-plus-with-marlin-2-0/)

Before starting, a word of caution. Despite performing better under Marlin 2.0 and allowing you to use a bed sensor, installing a new firmware can always lead to pretty big headaches and sometimes break your printer. Please make sure you know what you are doing before starting this tutorial.

## Arduino IDE & Marlin 2.0 Firmware Update

Steps 1 to 4 provide the environment you will need to upload Marlin 2.0 firmware onto your Anet A8 Plus.
1. Download Arduino IDE: https://www.arduino.cc/en/Main/Software. At the time of this tutorial, I am using Arduino IDE 1.8.12 .

2. Download the Skynet (still working) Anet A8 board definition: https://github.com/SkyNet3D/anet-board
   Follow the readme (i.e. place the hardware folder from the zip file inside My Documents\Arduino\hardware)

3. Download the Marlin bugfix-2.0.x archive on https://github.com/MarlinFirmware/Marlin/tree/bugfix-2.0.x

4. Download the Marlin bugfix-2.0.x configuration files archive on https://github.com/MarlinFirmware/Configurations/archive/bugfix-2.0.x.zip . Copy the .h files from \config\examples\Anet\A8plus into the \Marlin folder where the marlin.ino file is.

## Anet A8 Plus Configuration.h
To allow the Anet A8 Plus to run the Marlin 2.0 firmware and enable auto bed levelling you will need to modify the Configuration.h file.
You can download my version here. Do not forget to modify it according to the sensor you are using and its location over the bed.

5. Modify your local hardware to optimize the byte code as shown in https://thborges.github.io/blog/marlin/2019/01/07/reducing-marlin-binary-size.html . Find the file platform.local (from the Skynet hardware folder that you just copied inside My Documents\Arduino\Hardware\anet\avr) and add the following lines to it (normally it should just contain #EMPTY):

> compiler.c.extra_flags=-fno-tree-scev-cprop -fno-split-wide-types -Wl, - relax -mcall-prologues -finline-limit=3 -ffast-math

> compiler.cpp.extra_flags=-fno-tree-scev-cprop -fno-split-wide-types -Wl, - relax -mcall-prologues

> compiler.c.elf.extra_flags=-Wl, - relax

6. Open marlin.ino and inside the Arduino IDE select the Anet V1.0 (Optiboot) board in "Tools -> Board"

7. Modify the file Configuration.h to fit the sensor in your Anet A8 plus. Either un-comment the lines or set the right values

> #define FIX_MOUNTED_PROBE

> #define NOZZLE_TO_PROBE_OFFSET { your_x_value, your_y_value, 0 }

> #define XY_PROBE_SPEED 4000

> #define MULTIPLE_PROBING 3

> #define X_MIN_POS -26

> #define Y_MIN_POS -15

> #define Z_MIN_POS 0

> #define X_MAX_POS 285

> #define Y_MAX_POS 285

> #define Z_MAX_POS 370

> #define AUTO_BED_LEVELING_BILINEAR

> #define Z_SAFE_HOMING

## Building the Marlin 2.0 Firmware
Steps 8 and 9 help you upload the custom Marlin 2.0 firmware onto your printer.

8. Build the source by clicking on "Sketch -> Verify / Compile. If an error occur regarding a missing library like u8glib.h
 simply install it by clicking on "Sketch -> Include Library -> Manage libraries" and search for u8glib.

9. Upload the sketch to your printer by cliking on "Sketch -> Upload"

## Setting Up the Printer
Set the Z offset. Better than words, I recommend watching a very good video from Crosslink https://www.youtube.com/watch?v=gUr6kaOaRzo .
Also, do not forget to modify your Start and End Gcode in your slicer (available in anet_a8_plus_cura_gcode.txt).
