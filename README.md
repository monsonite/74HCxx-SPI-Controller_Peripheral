![image](https://github.com/user-attachments/assets/e9f71d7b-e067-453c-bfef-da45028a4067)



# 74HCxx-SPI-Controller_Peripheral
SPI controller and peripherals based on 74HCxx Logic

Interfacing to SPI peripheral and memory devices is easy if you have a microcontroller, a few lines of code either with an SPI peripheral or by GPIO bit-banging, but what if you wish to return to first principles and make use of discrete 74HCxx logic devices?

In SPI Controller 02 - above, a PROM U3 is used to store the data sequence to be sent to the SPI peripheral device. Because this sequencer has been derived from an earlier bit-serial processor, it draws heavily on that design for its operaion.

The main elements are:

Clock Sequencer quad 2 input NAND U1 74HC00 and 4-bit counter 74HC161 U2.

Program Counter formed from a pair of 74HC595 8-bit latching, tristate shift registers U4 and U5.  

Output Register U6. A parallel load 8-bit shift register, 74HC165 used to serialise the output data from the PROM ad send it to the peripheral via MOSI.

Input Register U7. A 74HC595 8-bit latching, tristate shift register used to receive external data transferred from the peripheral via MISO.

In order to convert a pair of shift registers into a 16-bit counter, you need a half-adder and a flipflop to hold any Carry that is produced. These are contained in U8, a 74HC283 full-adder, and U9, a 74HC74 dual D-type flipflop.

U10, a 74HC175, is a chain of four flipflops. They create additional "head and tail" pulses which are used by the clock sequencer.

U11, a 74HC00 provides gating of the serial clock and correcting of its polarity.

For the purpose of testing, the serial data output MOSI from U6 is connected directly to the serial data input MISO of U7.

The unused halves of U8 and U9 are used to form an ALU and Carry flipflop for the bit-serial CPU. These signals have been omitted for clarity.



