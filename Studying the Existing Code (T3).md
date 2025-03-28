## MODULE DECLRATION:-

![image](https://github.com/user-attachments/assets/18e87a84-ce92-43de-b952-086c2e6047c7)

(IMG SOURCE - https://github.com/thesourcerer8/VSDSquadron_FM/tree/main/uart_tx)

## TO NOTE:-

1. **CLK_FREQ**: Specifies the frequency of the input clock (default 50 MHz, common for many FPGAs).

2. **BAUD_RATE**: Sets the desired baud rate for the UART communication (default 9600 bits per second).

3. **DATA_WIDTH**: Defines the number of data bits to be transmitted (default 8 bits).

4. **Clk**: The system clock input.

5. **Reset**: An asynchronous reset signal.

6. **Data_in**: The parallel data to be transmitted.

7. **Load**: A control signal that triggers the start of the transmission.

8. Therefore, **tx** is the output signal.

## CLOCK DIVIDER:-

![image](https://github.com/user-attachments/assets/18e6aacb-7196-4db7-b646-f45308eb5ad5)

(IMG SOURCE - https://github.com/thesourcerer8/VSDSquadron_FM/tree/main/uart_tx)

## TO NOTE:-

1. **TICK_COUNT**: This parameter calculates the number of clock cycles required for one baud rate period. This is essential for generating the correct timing for the serial transmission.

2. **Tick_counter**: A 32-bit register that counts the clock cycles.

3. **Tick**: A single-bit register that generates a pulse (high for one clock cycle) every TICK_COUNT clock cycles. This pulse is used to synchronize the state machine.

![image](https://github.com/user-attachments/assets/cc3681b9-26bf-433d-a988-fd7fdb80ae9a)
![image](https://github.com/user-attachments/assets/95e1842f-9aef-43aa-9ea4-d93658c44fdc)
![image](https://github.com/user-attachments/assets/51abc3bb-b84d-47f7-80fe-c89fefbebfc2)
![image](https://github.com/user-attachments/assets/c3896b88-ce17-4dac-8fd5-4570acb7ef7d)

(IMG SOURCE - https://github.com/thesourcerer8/VSDSquadron_FM/tree/main/uart_tx)

## TO NOTE:-

1. **IDLE**: The transmitter is waiting for a load signal.

2. **START**: The start bit (logic 0) is transmitted.

3. **DATA**: The data bits are transmitted serially.

4. **STOP**: The stop bit (logic 1) is transmitted.

5. **STATE**: A 2-bit register that holds the current state of the state machine.

6. **SHIFT_REG**: A register that holds the data to be transmitted. The data is shifted out serially from the least significant bit (LSB).

7. **BIT_COUNT**: A counter that tracks the number of data bits transmitted.

8. The state machine controls the **UART** transmission process.

9. In the **IDLE** state, it waits for the load signal.

10. When load is asserted, it transitions to the **START** state and sends the start bit (0).

11. In the **DATA** state, it shifts out the data bits one by one, synchronized with the tick signal.

12. In the **STOP** state, it sends the stop bit (1) and returns to the **IDLE** state.

## SUMMARY:-

1. The code implements a standard **UART transmitter** with start, data, and stop bits.

2. The **clock divider** ensures accurate timing for the **serial transmission**

3. The **state machine**controls the **sequence of events**.

4. The **shift register** correctly serially **outputs the data**.

5. The code is **well-structured** and relatively easy to understand.


