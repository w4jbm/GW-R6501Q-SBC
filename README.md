# GW_R6501Q_SBC
Work related to Glitch Works R6501Q SBC Kit

## ROM Routies
Several of the routines in the eWoz monitor are useful when called from your own program.

* __PRBYTE ($FE24)__ - Prints the value in A in a 2 digit hexadecimal format. (A is clobbered.)
* __PRHEX ($FE38)__ - Prints the lower nibble (4 bits) of A as a 1 digit hexadecimal number. (A is clobbered.)
* __SHWMSG ($FE45)__ - Prints a NULL terminated string pointed to by Page Zero locations MSGL ($4B) and MSGH ($4C). (See the hw.a65 file for an example of how to use this.)
* __CIN ($FF2D)__ - Gets a character from the console and returns it in A.
* __COUT ($FF36)__ - Prints the character in A to the console.

Note: These locations may change if the monitor program is modified at some point down the road.

## Hello World
I did a quick rebuild of the ROM code to find some of the routines and will add more documentation later. This uses the ROM routine that sends a NULL terminated string to the console. It can be assembled with the online ASM80 dot COM assemlber for convience.
