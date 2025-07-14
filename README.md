# AM9511_for_TRS80_M2
Design of a PCB board that enables the use of a AM9511 Arithmetic Processing Unit (APU) on a TRS-80 Model II.
The AM9511 was developed by AMD.
Intel licensed it and created the Intel 8231.

A small board was developed for the TRS-80 Model II. The design is based on the sample circuits in the AM9511 documentation, with modifications needed to run in in a TRS-80 Model II. 

**Specifications**
-------------------------------------
Specifications of the board:
- I/O mapped on addresses 0CAh and 0CBh. This can be changed by reprogramming the GAL chip. Port CA was choosen because it was available and easy to remember: **CA**lculator
  - 0CAh  read and write of data
  - 0CBh  read of status and write of command
- Address decoding and RD*, WR*, CS* and RESET* signal generation is done using a GAL 22V10.
- Running on 2MHz with the option to connect an external clock signal.
- D0~D7 of the TRS-80 Model II bus are inverted. A 74LS240 takes care of converting it back.
- Jumpers are available for configuring the PAUSE* and END* signals.
- Testpoints are present for checking of the signals on the board.
- The board provides both +5V and +12V to the AM9511.

**Usage**
--------------------------------------
First use of the board is in I/O mode. This means that status is polled on port 0CBh. Write timing is influenced by the AM9511 by means of the PAUSE* signal that is connected to the Z80 WAIT* line.
A first check for operation/presense of the AM9511 is by repeatedly reading from port 0CAh. A read from the stack also results in placing the byte from the top of the stack at the bottom of the stack. The stack is 16 bytes deep.
This means that after reading 16 bytes, the data read from the stack is repeated. I have seen that after a reset most but not bytes are set to 0FFh.

**Important**
--------------------------------------
The AM9511 gets really hot during use. Consider placing a heatsick on the chip..

**Board design**
--------------------------------------
The board was designed using [Kicad](https://www.kicad.org/) version 6.4 and [Freerouting](https://github.com/freerouting/freerouting/) running on an ASUS Chromebook.

See [barberd/coco9511pak](https://github.com/barberd/coco9511pak) for support of the AM9511 on the Tandy Color Computer.


**Source and License**
-------------------------------------
The design is copyright 2024 by Ruud Broers. The design is open source, distributed via the GNU GPL version 3 license. Please see the LICENSE file for details.
