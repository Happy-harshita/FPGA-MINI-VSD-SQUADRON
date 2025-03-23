## PCF FILE:

![image](https://github.com/user-attachments/assets/d18f66e4-a406-4818-952b-ecf14aab3af3)

( IMG SOURCE - SHUTTERSTOCK.COM)

- Location: https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf

- Purpose: Defines FPGA pin assignments.

- Pin Assignments:

led_red -> Pin 39

led_blue -> Pin 40

led_green -> Pin 41

- hw_clk -> Pin 20

testwire -> Pin 17

## SIGNIFICANCE:

- led_red, led_blue, led_green:

- Verilog: Output signals for RGB LED control.

- PCF: Connect to FPGA pins wired to RGB LED.
Board: According to the VSDSquadron FPGA Mini datasheet (https://www.vlsisystemdesign.com/vsdsquadronfm/), Pins 39, 40, and 41 are connected to the red, blue, and green elements of the RGB LED, respectively.

## SIGNIFICANCE:
  
- Enables Verilog code to control the actual RGB LED on the board.

hw_clk:

- Verilog: Input signal for external clock.

- PCF: Connects FPGA pin 20 to clock source.

- Board: The datasheet confirms that Pin 20 is connected to the external hardware clock oscillator.

- Significance: Provides the clock signal to the FPGA, which is necessary for synchronous operation of the designed circuit.
testwire:

- Verilog: Output signal for observing an internal signal.

- PCF: Connects FPGA pin 17 to a point for signal observation.

- Board: The datasheet indicates that Pin 17 is connected to an I/O, which can be used to observe the signal.

## SIGNIFICANCE:

  - Allows monitoring of the behavior of the internal signal (testwire) for debugging or verification purposes.
    
## SUMMARY:

- The PCF file maps the logical signals defined in the Verilog code to the physical pins of the FPGA on the VSDSquadron board. This is essential for the FPGA to interact with the board's components as specified in the datasheet.
