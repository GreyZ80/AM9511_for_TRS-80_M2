# AM9511_for_TRS-80_M2
Design of a PCB board (started Jul 13, 2025) that enables the use of a AM9511 Arithmetic Processing Unit (APU) on a TRS-80 Model II.\
The AM9511 was developed by AMD. Intel licensed it and created the Intel 8231.\
The chips are not difficult to obtain (search iainventory.com or AliExpress.com for AM9511). So far I have not yet found fake chips 😃.

A small proto board was developed for the TRS-80 Model II. The design is based on the sample circuits in the AM9511 documentation, with modifications needed to use it in a TRS-80 Model II. Check the AM9511A Processor Manual for details. 
The ZIF socket pictured was used for easy swapping of AM9511 for chip testing. A final board would use a standard socket.

![AM9511 1st version](https://github.com/user-attachments/assets/2227b329-59b5-4784-bfa3-920fcfe2c156)


## Specifications

Specifications of the board:
- I/O mapped on addresses 064h (100 decimal) and 065h (101 decimal). This can be changed by reprogramming the GAL chip. Note that ports 0C0h and above are more or less reserved/in use on the TRS-80.
  - 064h  reading and writing of data
  - 065h  reading status and writing of commands
- Port address decoding and RD*, WR*, CS* and RESET* signal generation is done using a GAL 22V10. The .PLD file used for the compilation in WinCupl has been added. The .JED file can be used by a GAL programmer.
- Running on 3MHz with the option to connect an external clock signal.
- D0~D7 of the TRS-80 Model II bus are inverted on the bus. A 74LS640 takes care the conversion. This is an Octal <ins>Inverting</ins> Bus Transceiver (Tri-State)
- Jumpers are used for configuring the PAUSE* and END* signals.
- Testpoints are present for checking of the signals on the board.
- The board provides both +5V and +12V to the AM9511.

## Schematic

The schematic provides jumpers enabling various modes of operation.

<img width="500" alt="Schematic 0 2" src="https://github.com/user-attachments/assets/d3d50e7d-1868-47c7-be61-6cf8b30654e6" />


## Board design

The board was designed using [Kicad](https://www.kicad.org/) version 6.0 and [Freerouting](https://github.com/freerouting/freerouting/) running on my ASUS Chromebook.\
Port address decoding and signal inversion for RST is performed by a 22V10 GAL chip.

<img width="500"  alt="Arithmetic processor pcb 2024-07-31 23 44 08" src="https://github.com/user-attachments/assets/9c9f94f6-1f6c-4b05-b4f7-c461b02af106" />


## Usage

>[!CAUTION]
> **Do not touch the AM9511 APU during use, as it becomes really hot. Consider placing a heatsick on the chip.**

First use of the board is in I/O mode. This means that status is polled on port 065h. Write timing is influenced by the AM9511 by means of the PAUSE* signal when it is connected to the Z80 WAIT* line. For testing this is not needed and the Busy flag can be monitored at Port 065h.\
A first check for operation/presence of the AM9511 can be done by repeatedly reading from port 064h. A read from the stack not only puts the byte that is on top of the stack in register A, but also places the byte from the top of the stack at the bottom of the stack. Actually the stack pointer is moved. The stack is 16 bytes deep.
This means that after reading 16 bytes, the data read from the stack is repeated. I have seen that after a reset most, but not all, bytes are set to 0FFh.

The following Z80 assembler code performs a simple 16 bit addition.

```ruby
LD    C, 064H    ; data port  
LD    A, 34H     ; LSB of the 16 bit value 01234H  
OUT   (C),A  
LD    A, 12H     ; MSB
OUT   (C),A  
LD    A, 78H     ; LSB of the 16 bit value 05678H  
OUT   (C),A  
LD    A, 56H     ; MSB
OUT   (C),A  
LD    C,065H     ; command port  
LD    A, 06CH    ; Instruction to add 2 16 bit numbers  
LD    C,064H     ; data port  
IN    A,(C)      ; get MSB of the result (should be 068H)  
IN    A,(C)      ; get LSB of the result (should be 0ACH)  
```

Of course the same can be achieved using Basic. Just boot your machine and type Basic at the prompt.
```
100 rem ---- AM9511 ----
110 DP = &H64  :REM DATA PORT (64h)
120 CP = &H65  :REM Command/Status port (65h)
130 A = &H1234
140 B = &H5678
150 MSB = INT(A/256)
160 LSB = MOD(A,256)
170 OUT DP,LSB : OUT DP, MSB
180 MSB = INT(B/256)
190 LSB = MOD(B,256)
200 OUT DP,LSB : OUT DP, MSB
210 OUT CP,&H6C  :REM 06CH is the value of the instruction to add 2 16 bit numbers 
220 MSB = INP(DP) : LSB = INP(DP)
230 R = MSB * 256 + LSB
240 PRINT A;" +";B;" =";R


```





See [barberd/coco9511pak](https://github.com/barberd/coco9511pak) for support of the AM9511 on the Tandy Color Computer.


**Source and License**
-------------------------------------
The design is copyright 2024, 2026 by Ruud Broers. The design is open source, distributed via the GNU GPL version 3 license. Please see the LICENSE file for details.
