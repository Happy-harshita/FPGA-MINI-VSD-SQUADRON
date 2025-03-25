## BLOCK DIAGRAM:- 

![image](https://github.com/user-attachments/assets/ab1e7eff-48bd-43bd-ab9c-139c3c01f4ea)

(IMG SOURCE - SELF)

## EXPLANATION:-

- Serial Host: Represents the device sending and receiving serial data (e.g., a computer running a serial terminal program).

- FPGA (UART Loopback): Represents the FPGA configured to perform the UART loopback function.

- Serial Data (Tx): The data transmitted from the serial host to the FPGA's receive (rx) pin.

- Serial Data (Rx): The data transmitted from the FPGA's transmit (tx) pin back to the serial host.

- The diagram shows the clear flow of data from the serial host, to the FPGA, and back to the serial host.

  ## CIRCUIT DIAGRAM:-

  ![image](https://github.com/user-attachments/assets/e73f18da-617d-4b81-8743-0b8960df6f72)

  (IMG SOURCE - SELF)

  ## EXPLANATION:-

- The FPGA Mini connects to a computer via a USB-to-Serial converter for UART loopback.

- FPGA pins 14 (Tx) and 15 (Rx) link to the converter's Rx and Tx, respectively, with a shared ground.

- Voltage compatibility is key. 

- Serial terminal on the computer sends and receives data, validating the loopback.

  
