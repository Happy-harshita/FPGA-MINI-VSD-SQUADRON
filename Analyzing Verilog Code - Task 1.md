## VERILOG CODE

![image](https://github.com/user-attachments/assets/3ec96e81-7b63-408b-8df4-f274a308b0b3)

![image](https://github.com/user-attachments/assets/5b8ca1e9-9b7c-4f0c-8773-397131d4e061)

![image](https://github.com/user-attachments/assets/79bdfd3a-92ef-4570-9060-17ed04d5d2a9) 

![image](https://github.com/user-attachments/assets/bfa4462f-1600-450f-af39-0e9ae52fab2c)

![image](https://github.com/user-attachments/assets/ca8b0a2b-3e8e-4b1e-a589-f20db6ce0d38)

(IMG SOURCE - KUNAL SIR - EMAIL)

## MODULE DECLARATION :-

module top (

    // outputs
    
    output wire led_red  , // Red
    
    output wire led_blue , // Blue
    
    output wire led_green , // Green
    
    input wire hw_clk,  // Hardware Oscillator, not the internal oscillator
    
    output wire testwire
    
);

To summarize, this part of the code:

Defines a module named top. This is the main circuit we're describing, specifies the inputs and outputs of this circuit. Think of these as the connections to the outside world.

And here's a breakdown of the parts inside the parentheses:

- output wire led_red: Declares led_red as an output. It's a "wire" because it connects to something else. This output controls 
  the red LED.
  
- output wire led_blue: Same as above, but for the blue LED.
  
- output wire led_green: Same as above, but for the green LED.
  
- input wire hw_clk: Declares hw_clk as an input. It's a "wire" that brings in the clock signal.
  
- output wire testwire: Declares testwire as an output. This is for a test signal.
  
As a conclusion, this section sets up the connections for our circuit.  The top module has three outputs to control the RGB LED, one input for a clock, and one output for a test signal.

## TWO SIGNALS USED IN THE TOP MODULE
   
    reg  [27:0] frequency_counter_i;
    
- This section declares two signals that are used within the top module. Let's break them down:

- wire int_osc;

- wire: We've seen this before. It declares int_osc as a wire. As mentioned before, a wire is used to connect different parts of 
  a circuit. 

- It represents a physical connection. 

- The value of a wire is continuously assigned.

- int_osc: This is the name of the wire. It will carry the signal from the internal oscillator.

- reg [27:0] frequency_counter_i;

- reg: This declares frequency_counter_i as a register. 

- A register is a storage element that holds a value. Unlike a wire, a register's value is not continuously assigned; it only changes at specific times, usually at the edge of a clock signal.

- [27:0]: This specifies the width of the register. frequency_counter_i is a 28-bit register (from bit 27 down to bit 0). This 
  means it can hold values from 0 to 2<sup>28</sup> - 1.

- frequency_counter_i: This is the name of the register.

- It will be used to store the count of clock pulses.

- In essence, this part of the code declares a wire named int_osc and a 28-bit register named frequency_counter_i.

- The wire will carry the signal from the internal oscillator, and the register will store the count.

assign testwire = frequency_counter_i[5];

## ASSIGN STATEMENT

This is an **ASSIGN STATEMENT**. Here's what it does:

- Assign: This keyword is used to assign a value to a wire. Remember, wires are used to connect different parts of a circuit, and their values are continuously driven by assign statements.

- testwire: This is the wire we declared earlier as an output.

- frequency_counter_i[5]: This is where it gets interesting.

- frequency_counter_i: This is the 28-bit register we declared.

- [5]: This is a bit-select. It selects the 6th bit (bit number 5, since we start counting from 0) of the frequency_counter_i register.

- In simpler terms: This line of code connects the 6th bit of the frequency_counter_i register to the testwire output. So, the testwire output will reflect the value of that specific bit of the counter.

- Why the 6th bit?
  
- It's likely chosen to create a signal that changes at a specific frequency, which might be useful for testing or debugging purposes. As the counter increments, that bit will toggle between 0 and 1 at a rate that's slower than the clock but faster than if they had chosen bit 0.

## FREQUENCY COUNTER

always @(posedge int_osc) begin

    frequency_counter_i <= frequency_counter_i + 1'b1;
    
end

- This is a sequential block, which describes how the frequency_counter_i register changes over time. Let's break it down:

- always @(posedge int_osc):

- always: This keyword introduces a procedural block. There are two types of procedural blocks: always and initial. always blocks describe behavior that repeats continuously.

- @(posedge int_osc): This is the sensitivity list. It specifies when the code inside the always block should execute.
posedge: This means "positive edge".

- int_osc: This is the wire that carries the signal from the internal oscillator (our clock).

- Together, always @(posedge int_osc) means: "Execute the code inside this block every time the int_osc signal goes from 0 to 1" (i.e., on the rising edge of the clock).

- begin ... end: This is a block that groups one or more statements.

- frequency_counter_i <= frequency_counter_i + 1'b1;: This is the statement that executes on every rising edge of the clock:
frequency_counter_i: This is our 28-bit register.

- <=: This is the non-blocking assignment operator. In an always block, we use non-blocking assignments. All non-blocking assignments within the block are evaluated at the same time, and then the assignments are made.

- frequency_counter_i + 1'b1: This adds 1 to the current value of the frequency_counter_i register. 1'b1 is a 1-bit binary value of 1.

- In simpler terms: This code describes a counter. Every time the clock signal (int_osc) goes from low to high, the value in the frequency_counter_i register increases by 1.
 
## SB_HFOSC

SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));
  
- This line instantiates a module.

- Let's break it down:

- SB_HFOSC: This is the name of a predefined module. It represents a High-Frequency Oscillator, which is a circuit that generates a clock signal. This module is specific to the FPGA vendor (Lattice Semiconductor, in this case).

- #(...): This is the parameter assignment. It's used to configure the SB_HFOSC module.

- .CLKHF_DIV ("0b10"): This sets the CLKHF_DIV parameter of the SB_HFOSC module to the binary value "0b10" (which is decimal 2). This parameter likely controls the division of the oscillator's frequency. A value of 2 means the output frequency will be half of the base oscillator frequency.

- u_SB_HFOSC: This is the instance name. When you use a module, you need to give it a unique name. Here, we're calling this specific instance of the SB_HFOSC module u_SB_HFOSC. The u_ prefix is often used to indicate "instance".

- (...): This is the port connection list. It connects the signals in our top module to the inputs and outputs of the SB_HFOSC module.

- .CLKHFPU(1'b1): Connects the CLKHFPU input of the SB_HFOSC module to the constant value 1'b1 (binary 1). This likely enables a pull-up resistor for the oscillator.

- .CLKHFEN(1'b1): Connects the CLKHFEN input of the SB_HFOSC module to 1'b1, enabling the oscillator.

- .CLKHF(int_osc): Connects the CLKHF output of the SB_HFOSC module to the int_osc wire in our top module. This means the clock signal generated by the oscillator is now available on the int_osc wire.

- In simpler terms: This line of code adds a high-frequency oscillator to our circuit. We configure it to divide its output frequency by 2, enable it, and connect its output to the int_osc wire.

SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1),
    .RGB0PWM (1'b0), // red
    .RGB1PWM (1'b0), // green
    .RGB2PWM (1'b1), // blue
    .CURREN  (1'b1),
    .RGB0    (led_red), //Actual Hardware connection
    .RGB1    (led_green),
    .RGB2    (led_blue)
);
defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";
This section deals with controlling the RGB LED. Let's break it down:

## RGB'S

- SB_RGBA_DRV: This is another pre-defined module, likely provided by the FPGA vendor. It's a driver for an RGB LED.

- RGB_DRIVER: This is the instance name we've given to this particular use of the SB_RGBA_DRV module.

- (...): This is the port connection list, connecting signals in our top module to the inputs of the SB_RGBA_DRV module:
.RGBLEDEN(1'b1): Enables the RGB LED driver.

- .RGB0PWM(1'b0): Sets the PWM (Pulse Width Modulation) for the red LED to 0 (off).

- .RGB1PWM(1'b0): Sets the PWM for the green LED to 0 (off).

- .RGB2PWM(1'b1): Sets the PWM for the blue LED to 1 (on).

- .CURREN(1'b1): Enables current control for the LED.

- .RGB0(led_red): Connects the driver's red output to the led_red output of our top module.

- .RGB1(led_green): Connects the driver's green output to the led_green output of our top module.

- .RGB2(led_blue): Connects the driver's blue output to the led_blue output of our top module.

defparam ...: These lines set the parameters of the RGB_DRIVER instance:

- RGB0_CURRENT, RGB1_CURRENT, and RGB2_CURRENT: These parameters control the current for the red, green, and blue LEDs, respectively. The value "0b000001" sets a low current, which means the LED will be dim.

- In simpler terms: This code adds an RGB LED driver to our circuit.

- We configure it to turn on the blue LED (with a dim setting) and turn off the red and green LEDs.

- The driver's outputs are connected to the led_red, led_green, and led_blue outputs of our top module, which are connected to the actual pins of the FPGA that drive the physical LED.

  ## ENDMODULE

Endmodule

The endmodule keyword simply marks the end of the top module definition.  It tells the Verilog compiler that we're done describing the circuit.

## SUMMARY

1.We define a module named top.
2. We declare inputs and outputs (for the RGB LED, a clock, and a test signal).
3. We generate a clock signal using an internal oscillator.
4. We create a counter that increments with the clock.
5. We use an RGB LED driver to turn on the blue LED (dimly).
6. We output one bit of the counter for testing purposes.

