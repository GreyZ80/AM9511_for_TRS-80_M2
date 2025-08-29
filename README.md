# AM9511_for_TRS-80_M2
Design of a PCB board that enables the use of a AM9511 Arithmetic Processing Unit (APU) on a TRS-80 Model II.\
The AM9511 was developed by AMD. Intel licensed it and created the Intel 8231.\
The chips are not difficult to obtain (search AliExpress). So far I have not yet found fake chips 😃.

A small board was developed for the TRS-80 Model II. The design is based on the sample circuits in the AM9511 documentation, with modifications needed to use it in a TRS-80 Model II. Check the AM9511A Processor Manual for details.\
The ZIF socket pictured was used for easy swapping of AM9511 for chip testing. A final board would use a standard socket.

![AM9511 1st version](https://github.com/user-attachments/assets/2227b329-59b5-4784-bfa3-920fcfe2c156)


## Specifications

Specifications of the board:
- I/O mapped on addresses 0CAh and 0CBh. This can be changed by reprogramming the GAL chip. Port CA was choosen because it is not used and easy to remember: **CA**lculator
  - 0CAh  reading and writing of data
  - 0CBh  reading status and writing of commands
- Port address decoding and RD*, WR*, CS* and RESET* signal generation is done using a GAL 22V10.
- Running on 2MHz with the option to connect an external clock signal.
- D0~D7 of the TRS-80 Model II bus are inverted on the bus. A 74LS640 takes care of converting. This is an Octal Inverting Bus Transceiver (Tri-State)
- Jumpers are available for configuring the PAUSE* and END* signals.
- Testpoints are present for checking of the signals on the board.
- The board provides both +5V and +12V to the AM9511.

## Schematic

The schematic provides a lot of jumpers enabling various modes of operation.

<img width="500" alt="Schematic 0 2" src="https://github.com/user-attachments/assets/d3d50e7d-1868-47c7-be61-6cf8b30654e6" />


## Board design

The board was designed using [Kicad](https://www.kicad.org/) version 6.0 and [Freerouting](https://github.com/freerouting/freerouting/) running on my ASUS Chromebook.\
Port address decoding and signal inversion for RST is performed by a 22V10 GAL chip.

<img width="500"  alt="Arithmetic processor pcb 2024-07-31 23 44 08" src="https://github.com/user-attachments/assets/9c9f94f6-1f6c-4b05-b4f7-c461b02af106" />


Following is the source code for the GAL

```ruby
GAL Source code here  
```


## Usage

>[!CAUTION]
> **Do not touch the AM9511 APU during use, as it becomes really hot. Consider placing a heatsick on the chip.**

First use of the board is in I/O mode. This means that status is polled on port 0CBh. Write timing is influenced by the AM9511 by means of the PAUSE* signal that is connected to the Z80 WAIT* line.
A first check for operation/presense of the AM9511 can be done by repeatedly reading from port 0CAh. A read from the stack not only puts the byte that is on top of the stack in register, but also places the byte from the top of the stack at the bottom of the stack. The stack is 16 bytes deep.
This means that after reading 16 bytes, the data read from the stack is repeated. I have seen that after a reset most, but not all, bytes are set to 0FFh.

The following Z80 assembler code performs a simple 16 bit addition.

```ruby
LD    C, 0CAH    ; data port  
LD    A, 34H     ; LSB of the 16 bit value 01234H  
OUT   (C),A  
LD    A, 12H  
OUT   (C),A  
LD    A, 78H     ; LSB of the 16 bit value 05678H  
OUT   (C),A  
LD    A, 56H  
OUT   (C),A  
LD    C,0CBH     ; command port  
LD    A, 06CH    ; Instruction to add 2 16 bit numbers  
LD    C,0CAH     ; data port  
IN    A,(C)      ; get MSB of the result (should be 0ACH)  
IN    A,(C)      ; get LSB of the result (should be 068H)  
```





See [barberd/coco9511pak](https://github.com/barberd/coco9511pak) for support of the AM9511 on the Tandy Color Computer.


**Source and License**
-------------------------------------
The design is copyright 2024 by Ruud Broers. The design is open source, distributed via the GNU GPL version 3 license. Please see the LICENSE file for details.
