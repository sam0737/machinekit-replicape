Replicape Configuration for MachineKit
======================================

This is to make Replicape usable in Machinekit, essentially this contains:
* A sample HAL linking up the hardware and GPIO
* Python HAL module for PWM controlling (Replicape uses a dedicated PWM controller)
* Python HAL module for Stepper configuration such as Enable, Microstepping, Decay, and DAC configuration for the stepper current settings.

This is developed based on the CRAMPS configuration that comes with the Machinekit.  This uses the same generic PRUSS firmware that comes with Machinekit/CRAMPS in case you are interested.

Features
--------

* Support QuickQtVcp for controlling, instead of LinuxCNC's Axis
  * Get a copy of Machineface on the server: https://github.com/strahlex/Machineface
  * And machineface on the client: https://github.com/strahlex/MachinekitClient/releases/
  * (Or any QuickQtVcp compatible interface)
* Velocity Extrusion (http://blog.machinekit.io/2014/05/velocity-driven-extrusion.html)

Prerequisite 
------------
* Machinekit (of course)
* Device Overlay Tree for Replicape
See README in Redeem for details https://bitbucket.org/intelligentagent/redeem/src/
* Python Module spi, smbus
* Get a copy of RS274 subroutines at 
  https://github.com/thecooltool/Uni-print-3D/tree/ve2/subroutines
* A special of Slic3r needed: https://github.com/strahlex/Slic3r/tree/velocity-extrusion.
  See https://groups.google.com/forum/#!msg/machinekit/dsMETL-6_yM/8RxQiBfb2wsJ for more info.

Usage
-----

* In run.py, 
  * Change the Video Streaming path accordingly
  * Change the Machineface path accordingly
* Refer to the RS274 subroutines in replicape.ini
* Slic3r configuration, under Printer Settings:
  * use firmware retraction + velocity extrusion
  * Put ```M200 D[filament_diameter]``` in Start G-code in Slic3r
* Be sure to go through the HAL, INI. The scaling, endstops, kins, stepper connections, fans, microsteppings, etc.

Lincese
-------
The MIT License (MIT)

Copyright (c) 2015 Sam Wong

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
