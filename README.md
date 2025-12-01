![image](https://github.com/user-attachments/assets/e9f71d7b-e067-453c-bfef-da45028a4067)



# 74HCxx-SPI-Controller_Peripheral
SPI controller and peripherals based on 74HCxx Logic

Interfacing to SPI peripheral and memory devices is easy if you have a microcontroller, a few lines of code either with an SPI peripheral or by GPIO bit-banging, but what if you wish to return to first principles and make use of discrete 74HCxx logic devices?

A typical SPI exchange consists of a synchronous transfer of serial data from the controller to the peripheral and a simultaneous return transfer from the peripheral back to the contoller. This is often, but not limited, to 8-bit packets.

The bidirectional transfer is performed using a pair of shift registers, and parallel to serial conversion is often done at the same time. The output shift register on the master, can be a 74xx165 parallel in, serial out (PISO) register. The input register on the peripheral can be a 74xx595, a latching serial in, parallel out register. If a true full duplex, bidirectional exchange of data is required, then either an input and output register will be needed at both ends, or a single bidirectional register, like the tristate bus 74xx299 can be used.

SPI is often used for sensors, GPIO expanders, ADCs, DACs and serial memory devices. It is characterised by only requiring 4 signals:

/CS An active low chip enable signal, to select the peripheral device. Multiple peripherals will each need their own enable signal /CS0,  /CS1, /CS2 etc.

MOSI -  the serial output from the controller to the input of the peripheral

MISO - the serial input from the peripheral to the controller

SCLK - the synchronous serial clock to accompany the serial data.

As the controller device generates the SCLK and this clock is sent to the peripheral, they will remain in synchronisation and the actual frequency, or timing of the serial data is not important.

The transfer begins by the /CS signal being asserted low. This enables the peripheral, taking it out of low power mode, and puts it into data transfer mode. The output register is enabled for data transfer onto the MISO pin.

Following on from the assertion of /CS, a series of gated clock pulses will be sent on SCLK and accompanying data on the MISO line.

The idle state of the SCLK may be chosen to be high or low, this is known as CPOL (clock polarity). If CPOL=0 the idle state of the clock is low. If CPOL=1 the idle state of the clock is high.

CPHA (clock phase) refers to the clock edge on which data is clocked into the register. If CPHA is 0, the data is transmitted on the rising edge of the clock. Data is transmitted on the falling edge when CPHA is 1.

These combinations of CPOL and CPHA give 4 possible operating modes, and sime peripherals support more than one mode.

In this example Mode 0 will be used. CPOL = 0 (low idle state) and CPHA = 0 (clock data on rising edge).

In addition to the shift registers, a simple sequencer, or state machine, based on a counter is used to generate the control signals /CS and SCLK.

<img width="1502" height="830" alt="image" src="https://github.com/user-attachments/assets/3d2ea0f2-5ca7-405b-8ee3-d805baa22c8c" />

In SPI Controller 1 - above, a 74HC4017 decoded, decade counter, U1 provides a series of timing pulses, T0, T1 and T9. 

T0 is used to load the PISO shift register U2 with the parallel data from the User Switches. This could be any source of parallel data - such as a ROM or a RAM.

T1 is used to assert the /CS chip select pulse, using the SR latch, U4.

T9 is used to de-assert the /CS signal.

The GATE output of the SR latch is used to gate a burst of 8 clock pulses to form the SCLK signal.

On pressing the STEP button, the data is latched into the MOSI output shift register and then transferred to the input shift register, 74xx495 U3.

The rising edge of /CS is used to clock the received data into the latch section of U3.





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



