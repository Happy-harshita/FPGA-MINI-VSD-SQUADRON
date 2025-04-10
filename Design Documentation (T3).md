## BLOCK DIAGRAM:-

![image](https://github.com/user-attachments/assets/2f708ef0-dad9-4ba8-b1a4-a187402ecfb7)

(IMG SOURCE - SELF)

- **Input Data Reg.**: This is the 8-bit register that holds the data to be transmitted.

- **Clock Divider (Baud Rate Gen.)**: This module takes the system clock and divides it to generate the clock signal needed for the desired baud rate.

- **State Machine (Control Logic)**: This is the heart of the UART transmitter. It manages the sequence of operations (idle, start, stop bit) using a state machine.

- **Shift Register (Parallel to Ser.)**: This register takes the parallel data from the Input Data Register and converts it into a serial stream of bits for transmission.

- **TX Output (tx_out)**: This is the output pin of the FPGA that carries the serial data.

- **Tick Counter**: This counter is driven by the clock signal from the Clock Divider. It provides the timing for each bit transmission, ensuring the correct baud rate.

-  The **tick signal** from the Clock Divider is  be helpful to include this signal as an explicit connection to the State Machine and Shift Register in the diagram itself. This would visually reinforce the timing aspect.

- **Reset signal** might be helpful to show the reset signal going to all the boxes that it resets.

- The **load signal** might be helpful to show the load signal going to the Input Data Reg box.

![image](https://github.com/user-attachments/assets/405878bb-2158-4454-9cf9-3140466c60e8)

(IMG SOURCE - SELF)

- The PC, acting as the receiving device, utilizes a **serial terminal application** to observe and interpret the data transmitted by the FPGA.

- In modern setups, a **USB-to-serial converter typically bridges the connection**, translating the FPGA's UART serial output, characterized by logic levels such as 3.3V, into a USB signal compatible with the PC.

- This converter also handles **necessary voltage level shifting**, eliminating the need for direct RS-232 connections.

- The serial terminal software, such as PuTTY or Tera Term, running on the PC, **emulates a serial port**, allowing users to **configure critical communication parameters** like baud rate, data bits, parity, and stop bits.

- Accurate matching of these settings with the FPGA's UART configuration is paramount for successful data **reception and display**.

- Essentially, the PC and its serial terminal provide the **interface for verifying and analyzing the serial data** stream generated by the FPGA's UART transmitter.
