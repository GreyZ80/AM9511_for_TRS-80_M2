This folder contains AM9511 datasheets and information that I collected from the internet.

In the Basic folder there is a quick and dirty test program for the AM9511. It assumes the FPU is mapped to ports 100 (data) 101 (commands/status).
The program shows the full stack and allows entering operands and commands by pressing F1 (Operand) or F2 (Command).\
Commands are entered by typing-in their mnemonic.
Note that running the code using a Basic interpreter is not really fast. The AM9511 busy flag is only detected for complex operations that take a relatively long time to complete.

Notes:
- **Am9511 FPU datasheet.pdf** does not preview in Github, but is OK when downloaded.\
- **1980_AMD_PUB098_AmZ8000_Family_Data_Book.pdf** does not preview in Githib, but is OK when downloaded. On page 4-119 there is an extensive chapter on the AM9511A. 
