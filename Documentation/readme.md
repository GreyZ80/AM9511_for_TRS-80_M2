This folder contains AM9511 datasheets and information that I collected from the internet.

In the Basic folder there is a quick and dirty test program for the AM9511. It assumes the FPU is mapped to ports &hCA (data) &hCB (commands/status).
The program shows the full stack and allows entering operands and commands.\
Commands are enter by typing in their mnemonic.
Note that running the code using a Basic interpreter is not really fast. The AM9511 busy flag is only detected for complex operations that take a relatively long time to complete.
