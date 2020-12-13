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

I noticed the the ROMFS upgrade was available in early December 2020 and upgraded the system. I'm having a bit of trouble because by default there is a value OutPad that is set to $82. The sign bit indicates that with each CR/LF, OUTPAD should use $7F (rubout) as the pad character and send two of them (the value in the lower nibble). Running minicom under Linux, this causes a pair of stray spaces to appear. I'm going to see if I can locate and patch things.

## Revision History
10-29-2020: Original commit.
12-12-2020: Upgrade to ROMFS.

