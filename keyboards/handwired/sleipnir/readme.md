Sleipnir 
=====

![MiniDox](http://i.imgur.com/iWb3yO0.jpg)

A finger length optimised keyboard base on the Minidox.

This readme is a direct copy of the Minidox readme with only the most necessary changes.

The reason for this is that Sleipnir was created in order to address a desire by lehoff to have a slimmer keyboard for travelling. As investigations revealed that the most suitable key switches weren't compatible with the existing Minidox PCB lehoff's good friend OmerK volunteered to design a new PCB. Since a new PCB was needed a more customised placement of the columns was included. Basically lehoff has oddly long middle and ring fingers so in particular the placement of the ring finger column is different from most other keyboards: it is shifted down about half a key compared to the Minidox. Furthermore, an extra thumb key has been added to allow more flexible modifier key assignments and all the modifier keys are now 1U keys as the bigger 1.5U for one of them didn't really make any difference while using it.

OmerK is quite the hardware wizard so the Pro Micro / has been replaced by just the CPU, all in the interest of making the keyboard as thin as possible.

Keyboard Maintainer: lehoff 
Hardware Supported: prototype.

Make example for this keyboard (after setting up your build environment):

    make sleipnir/rev1:default

See [build environment setup](https://docs.qmk.fm/#/getting_started_build_tools) then the [make instructions](https://docs.qmk.fm/#/getting_started_make_guide) for more information.

## Build Guide

A build guide for putting together the sleipnir v1 can be found here: [Sleipnir Build Log / Guide](http://pap.com)

Flashing
-------
Note: Most of this is copied from the Let's Split readme, because it is awesome

From the root directory run `make PROJECT:KEYMAP:avrdude` for automatic serial port resolution and flashing.
Example: `make sleipnir/rev1:default:avrdude`

Choosing which board to plug the USB cable into (choosing Master)
--------
Because the two boards are identical, the firmware has logic to differentiate the left and right board.

It uses two strategies to figure things out: look at the EEPROM (memory on the chip) or looks if the current board has the usb cable.

The EEPROM approach requires additional setup (flashing the eeeprom) but allows you to swap the usb cable to either side.

The USB cable approach is easier to setup and if you just want the usb cable on the left board, you do not need to do anything extra.

### Setting the left hand as master
If you always plug the usb cable into the left board, nothing extra is needed as this is the default. Comment out `EE_HANDS` and comment out `I2C_MASTER_RIGHT` or `MASTER_RIGHT` if for some reason it was set.

### Setting the right hand as master
If you always plug the usb cable into the right board, add an extra flag to your `config.h`
```
 #define MASTER_RIGHT
```

### Setting EE_hands to use either hands as master
If you define `EE_HANDS` in your `config.h`, you will need to set the
EEPROM for the left and right halves.

The EEPROM is used to store whether the
half is left handed or right handed. This makes it so that the same firmware
file will run on both hands instead of having to flash left and right handed
versions of the firmware to each half. To flash the EEPROM file for the left
half run:
```
avrdude -p atmega32u4 -P $(COM_PORT) -c avr109 -U eeprom:w:"./quantum/split_common/eeprom-lefthand.eep"
// or the equivalent in dfu-programmer

```
and similarly for right half
```
avrdude -p atmega32u4 -P $(COM_PORT) -c avr109 -U eeprom:w:"./quantum/split_common/eeprom-righthand.eep"
// or the equivalent in dfu-programmer
```

NOTE: replace `$(COM_PORT)` with the port of your device (e.g. `/dev/ttyACM0`)

After you have flashed the EEPROM, you then need to set `EE_HANDS` in your config.h, rebuild the hex files and reflash.

Note that you need to program both halves, but you have the option of using
different keymaps for each half. You could program the left half with a QWERTY
layout and the right half with a Colemak layout using bootmagic's default layout option.
Then if you connect the left half to a computer by USB the keyboard will use QWERTY and Colemak when the
right half is connected.
