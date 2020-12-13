# GW_R6501Q_SBC
Work related to Glitch Works R6501Q SBC Kit. The R6501Q is from the 6502 family, but has I/O and memory built in to Page Zero. The stack is also located in Page Zero, so things get cozy in terms of finding space to port some software over.

## ROM Routies
Several of the routines in the eWoz monitor are useful when called from your own program.

* __SFTRST ($FD34)__ - This performs a soft reset for the monitor. It can be used to return to the monitor if the stack might have been corrupted. (If you know the stack is back to its original condition, a program can exit and return from a RUN command with an RTS.)
* __PRBYTE ($FE24)__ - Prints the value in A in a 2 digit hexadecimal format. (A is clobbered.)
* __PRHEX ($FE38)__ - Prints the lower nibble (4 bits) of A as a 1 digit hexadecimal number. (A is clobbered.)
* __SHWMSG ($FE45)__ - Prints a NULL terminated string pointed to by Page Zero locations MSGL ($4B) and MSGH ($4C). (See the hw.a65 file for an example of how to use this.)
* __CIN ($FF2D)__ - Gets a character from the console and returns it in A. (Does not return until a character is available.)
* __COUT ($FF36)__ - Prints the character in A to the console.

Note: These locations may change if the monitor program is modified at some point down the road.

## Hello World
I did a quick rebuild of the ROM code to find some of the routines and will add more documentation later. This uses the ROM routine that sends a NULL terminated string to the console. It can be assembled with the online ASM80 dot COM assemlber for convience.

![Hello World](https://github.com/w4jbm/GW_R6501Q_SBC/raw/main/hw_25Oct2020.png)


## Tiny BASIC

I noticed the the ROMFS upgrade was available in early December 2020 and upgraded the system. I did have a bit of trouble because by default, Tiny BASIC has a value labeled OutPad that is set to $82. This is used to determine if and how a CR/LF is to be "padded". (A remanent of the teletype days...)

With OutPad set to $84, the sign bit indicates that with each CR/LF, OUTPAD should use $7F (rubout) as the pad character and send two of them (the value in the lower nibble). Running minicom under Linux, this causes a pair of "stray blank spaces" to appear. I was able to the file to set OutPad to $00 which says padding should be a NULL character (because the sign bit is clear) and there should be zero of them sent.

Another quirk I ran into as I tinkered with this is that after the command prompt (a ":"), Tiny BASIC also sends a Control-Q (hex $11, also the x-on character) to clear things up in a previous Control-S (x-off) had been sent. This didn't seem to have any effect.

I patched a version of Tiny BASIC that is here in Intel Hex format. Under Linux I used `hexedit` to search for 8280 and then replaced the $82 with a $00. I converted this to a .hex file using `srec_cat tb_patched.bin -binary -offset 0x1000 -output tb_patched.hex -Intel`. (The patched version included here saves you the trouble.) You can load it and then use `1000R` to start it (which is easier for me than messing with the switches anyway). If you reset and get out of Tiny BASIC and into eWoz, you can do a warm start that will retain your program unless you've done something to modify memory by entering `1003R`.

After some thinking, I also found an easy way to just patch the proper byte during the upgrade process. There are no ill effects if you upgrade an already upgraded system...

Follow the instruction on loading the `updater_conf2.hex` file using eWoz, but don't run it yet. In the current code (as of 13 Dec 2020) I found the location in memory that mapped to what ultimately becomes OutPad at $168B. In eWoz, typing `168B` should show the memory contents of that location as $82. You can patch by typing `168B:00`. Once that's done, verify the change to the memory location was made and then continue with the instructions for the upgrade by typing `200R` to run the ROM upgrade code. After the upgrade, things work fine with minicom under Linux.


## Revision History
* 10-29-2020: Original commit.
* 12-12-2020: Upgrade to ROMFS. Patch for Tiny BASIC.
