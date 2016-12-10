Replicape Configuration for MachineKit
======================================

This is to make Replicape usable in Machinekit, essentially this contains:
* Python HAL script linking up the hardware, GPIO, velocity extrusion controlling and exposing remote UI HAL.
* Python HAL module for PWM controlling (Replicape uses a dedicated PWM controller)
* Python HAL module for Stepper configuration such as Enable, Microstepping, Decay, and DAC configuration for the stepper current settings.
* Reprap GCode remap

Once fully setup, one would be able to control the Replicape in MachineKit way

Specifically one could,
* Install and use any QuickQtVcp user interface, such as Machineface - designed for 3D Printer. (https://github.com/machinekoder/Machineface)
* Use any MachinekitClient to connect to Machineface (https://github.com/strahlex/MachinekitClient/)

This is developed based on the CRAMPS configuration that comes with the Machinekit.  This uses the same generic PRUSS firmware that comes with Machinekit/CRAMPS in case you are interested.

Note 
------------

As of this writing, I am using 2016-11 version of Machinekit image. Please note that machinekit still has not reached 1.0 and a fast moving target, some instructions might not work in the future version.

Prerequisite 
------------

### BeagleBoard Black software preparation

* Machinekit on BeagleBoard Black
  * A prebuilt debian image with machinekit installed is available at http://elinux.org/Beagleboard:BeagleBoneBlack_Debian
    The "microSD/Standalone: (machinekit) Based on Debian Jessie (new)" version is recommended
  * Windows user could use https://rufus.akeo.ie/ to write the image to the MicroSD card. Linux user could just DD to the MicroSD card.
  * After flashing the MicroSD card with the image, one could convert it to a "Flasher" to flash the onboard eMMC instead.
    Instruction of modifying the MicroSD files to the flasher mode is at http://elinux.org/Beagleboard:BeagleBoneBlack_Debian#Flashing_eMMC
* Knowing how to login and secure the BBB (Not Replicape specific but general instruction for all BBB user)
  * SSH into the board with machinekit. If you power up the BBB with USB, the BBB should present itself as a USB Ethernet device to the host computer. One should be able SSH to 192.168.7.2.
  * If you have the Ethernet connected, check what dynamic IP got assigned to the BBB
  * The default user and password is ```machinekit``` and ```machinekit```
  * One might want to change the password, optionally create a new user, and configuring SSH pubkey authorization for easier maintainence, and changing the hostname.
* Upgrading to kernel 4.4 (The instructions is not tested for other kernel)
```
cd /opt/scripts/tools
sudo ./update_kernel.sh --lts-4_4 --bone-rt-channel
reboot
```
* Install the Device Tree Overlays (such that the Replicape can be recognized)
  * Follow the instruction at https://github.com/beagleboard/bb.org-overlays
```
git clone https://github.com/beagleboard/bb.org-overlays
cd ./bb.org-overlays
./dtc-overlay.sh 
./install.sh
```
  * Reboot with the Replicape plugged onto BBB
  * Verify if it is working
```
# If the cape is plugged and power-on, it should be detected
cat /sys/devices/platform/bone_capemgr/slots
 0: P-----  -1 Replicape 3D printer cape,0B3A,Intelligent Agen,BB-BONE-REPLICAP
 1: PF----  -1
 2: PF----  -1
 3: PF----  -1
 4: P-O-L-   0 Override Board Name,00A0,Override Manuf,cape-universaln

# Check if the devices are populated, if this directory is not found, read on...
ls /sys/bus/iio/devices/iio:device0/

# Check if you got the following error message
dmesg | less
[    5.506020] bone_capemgr bone_capemgr: loader: failed to load slot-0 BB-BONE-REPLICAP:0B3A (prio 0)
```
    * It is known that the overlay tree does not have data for Rev 0B3A, but Rev 00B3. So let's flash the 00B3 info into the eeprom
```
https://bitbucket.org/intelligentagent/replicape/raw/bf08295bbb5e98ce6bff60097fe9b78d96002654/eeprom/Replicape_00B3.eeprom
cat Replicape_00B3.eeprom > /sys/bus/i2c/devices/*-0054/eeprom
```
    * Now the ```bone_capemgr/slots``` should shows ```0B3A```, and ```iio:device0``` should be present after reboot.
```
dmesg | less
[    4.533048] bone_capemgr bone_capemgr: slot #0: dtbo 'BB-BONE-REPLICAP-00B3.dtbo' loaded; overlay id #0
```
* Install the following python modules, which are used in the HAL
```
pip install spi smbus
```
* Clone this repository for the RS274 subroutines and HAL files
```
# Do this non-root is highly recommended
cd ~
git clone https://github.com/sam0737/machinekit-replicape
```
* Clone the Machineface
```
# Do this non-root is highly recommended
cd ~
git clone https://github.com/machinekoder/Machineface
```

### Others

* Slicer: Slic3r 1.2.9 is needed. Only since that version velocity extrusion is supported. Other slicers are not tested.
* Get a machinekit client on the host computer at https://github.com/strahlex/MachinekitClient/

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
