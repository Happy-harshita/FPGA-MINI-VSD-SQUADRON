## INPUT AND OUTPUT SIGNALS:-
- Identify the rx (receive) and tx (transmit) signals. These are the core signals for the UART communication.

- Look for the clk (clock) and rst (reset) signals, which are essential for the FPGA's operation.

![image](https://github.com/user-attachments/assets/7c1a9e9d-a0ba-4daa-85ab-65927dce8023)

(IMG SOURCE - https://www.bing.com/search?q=CLK%2F%20reset%20&qs=n&form=QBRE&sp=-1&lq=0&pq=clk%2F%20reset%20&sc=0-11&sk=&cvid=5385B1C2310849EBBE51BFC7A301C8F5&ghsh=0&ghacc=0&ghpl=)

- The simplest loopback logic involves directly assigning the rx signal to the tx signal: assign tx = rx.
  
- However, the actual code might include more complex logic for baud rate generation, data synchronization, and potential buffering.

## BAUD RATE GENERATION:-
- UART communication requires precise timing.

- The code might contain a counter and comparison logic to generate the necessary timing signals for the specified baud rate.

- Look for how the code derives the proper bit timing from the FPGA's clock.

  ![image](https://github.com/user-attachments/assets/2031c474-ed45-48f2-b7fb-312b26ba816c)

(IMG SOURCE - https://www.bing.com/search?pglt=41&q=baude+rate+generation&cvid=5a1edb6c980e4836b1b450c63778df3a&gs_lcrp=EgRlZGdlKgYIABBFGDkyBggAEEUYOTIICAEQ6QcY_FXSAQg5NTMxajBqMagCALACAA&FORM=ANNAB1&PC=DCTS)

## IMPORTANT POINTS:- 

1. Signal Tracing: Follow the path of the rx signal through the code to see how it's processed and then assigned to the tx signal.

2. Timing Analysis: Examine the code that generates the baud rate timing. 

3. Synchronization Analysis: Identify the flip-flops used for synchronization

4. Parameter Analysis: Examine all the parameters to understand the values that are being used within the code.

## VERILOG CODE.

`include "uart_trx.v"

//----------------------------------------------------------------------------

//                                                                          --

//                         Module Declaration                               --

//                                                                          --

//----------------------------------------------------------------------------

module top (

  // outputs
  
  output wire led_red  , // Red
  
  output wire led_blue , // Blue
  
  output wire led_green , // Green
  
  output wire uarttx , // UART Transmission pin
  
  input wire uartrx , // UART Transmission pin
  
  input wire  hw_clk

);

  wire        int_osc            ;

  reg  [27:0] frequency_counter_i;
  
    
//----------------------------------------------------------------------------

//                                                                          --

//                       Internal Oscillator                                --

//                                                                          --

//----------------------------------------------------------------------------

  SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));

  assign uarttx = uartrx;

//----------------------------------------------------------------------------

//                                                                          --

//                       Counter                                            --

//                                                                          --

//----------------------------------------------------------------------------

  always @(posedge int_osc) begin

    frequency_counter_i <= frequency_counter_i + 1'b1;
  
        /* generate 9600 Hz clock */
  
  end

//----------------------------------------------------------------------------

//                                                                          --

//                       Instantiate RGB primitive                          --

//                                                                          --

//----------------------------------------------------------------------------

  SB_RGBA_DRV RGB_DRIVER (
  
    .RGBLEDEN(1'b1                                            ),
    
    .RGB0PWM (uartrx),
    
    .RGB1PWM (uartrx),
    
    .RGB2PWM (uartrx),
    
    .CURREN  (1'b1                                            ),
    
    .RGB0    (led_green                                       ), //Actual Hardware connection
    
    .RGB1    (led_blue                                        ),
    
    .RGB2    (led_red                                         )
  
  );
  
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";


endmodule

## TO NOTE:-

1. This Verilog code sets up a simple loopback for serial communication on an FPGA.

2. When the FPGA receives serial data on the uartrx pin, it immediately sends that same data back out on the uarttx pin.

3. Additionally, it uses the received serial data to control the brightness of an RGB LED. 

4. The internal oscillator is used to clock a counter, and the RGB LED driver is used to control the LEDs. 

5. The code provides a basic way to test serial communication and visualize incoming data through the RGB LED.

## UART LOOP BACK CODE:-
// 8N1 UART Module, transmit only

module uart_tx_8n1 (
    
    clk,        // input clock
    
    txbyte,     // outgoing byte
    
    senddata,   // trigger tx
    
    txdone,     // outgoing byte sent
    
    tx,         // tx wire
    
    );

    /* Inputs */
    
    input clk;
    
    input[7:0] txbyte;
    
    input senddata;

    /* Outputs */
    
    output txdone;
    
    output tx;

    /* Parameters */
    
    parameter STATE_IDLE=8'd0;
    
    parameter STATE_STARTTX=8'd1;
    
    parameter STATE_TXING=8'd2;
    
    parameter STATE_TXDONE=8'd3;

    /* State variables */
    
    reg[7:0] state=8'b0;
    
    reg[7:0] buf_tx=8'b0;
    
    reg[7:0] bits_sent=8'b0;
    
    reg txbit=1'b1;
    
    reg txdone=1'b0;

    /* Wiring */
    
    assign tx=txbit;

    /* always */
    
    always @ (posedge clk) begin
    
        // start sending?
        
        if (senddata == 1 && state == STATE_IDLE) begin
        
            state <= STATE_STARTTX;
            
            buf_tx <= txbyte;
            
            txdone <= 1'b0;
        
        end else if (state == STATE_IDLE) begin
        
            // idle at high
            
            txbit <= 1'b1;
            
            txdone <= 1'b0;
        
        end
        
        // send start bit (low)
        
        if (state == STATE_STARTTX) begin
        
            txbit <= 1'b0;
            
            state <= STATE_TXING;
        
        end
        
        // clock data out
        
        if (state == STATE_TXING && bits_sent < 8'd8) begin
        
            txbit <= buf_tx[0];
            
            buf_tx <= buf_tx>>1;
            
            bits_sent = bits_sent + 1;
        end else if (state == STATE_TXING) begin
            
            // send stop bit (high)
            
            txbit <= 1'b1;
            
            bits_sent <= 8'b0;
            
            state <= STATE_TXDONE;
        
        end
        
        // tx done
        
        if (state == STATE_TXDONE) begin
        
            txdone <= 1'b1;
            
            state <= STATE_IDLE;
        
        end
        
        end

endmodule

## TO NOTE:-

1. The uart_tx_8n1 Verilog module is a simple UART transmitter for 8N1 serial data.

2. The transmission includes a start bit, the data bits, and a stop bit.

3. For a loopback setup, the module's tx output is connected to a receiver's uartrx input.

4.  This allows for verification of the serial communication.

5.   However, this code lacks baud rate generation, so both transmitter and receiver must operate with synchronized timing.

6.   The module outputs a txdone signal when the transmission is complete. The current bit being sent is stored in the txbit register.

## ADDING UART LOOPBACK EXAMPLE:-

set_io  led_green 40

set_io  led_red 39

set_io  led_blue 41

set_io  uarttx 14

set_io  uartrx 15

set_io  hw_clk 20

## TO NOTE:-

1. They define the physical pin assignments for specific signals on the FPGA.

2. The set_io command maps logical signals from the Verilog code to physical pins on the FPGA package.

3. Specifically, led_green is assigned to pin 40, led_red to pin 39, and led_blue to pin 41, controlling an RGB LED.

4. The uarttx and uartrx signals, used for serial communication, are assigned to pins 14 and 15, respectively. 

5. Finally, hw_clk, the hardware clock input, is assigned to pin 20.

## RENAMING TOP MODULE, FIXING BAUDRATE:- 

TOP=top

PCF_FILE=VSDSquadronFM

BOARD_FREQ=12

CPU_FREQ=20

FPGA_VARIANT=up5k

FPGA_PACKAGE=sg48

VERILOG_FILE=top.v

#Uart Var	

PICO_DEVICE=/dev/ttyUSB0   # replace by the terminal used by your device

BAUDS=9600

build:

 yosys -DCPU_FREQ=$(CPU_FREQ) -q -p "synth_ice40 -abc9 -device u -dsp -top $(TOP) -json $(TOP).json" $(VERILOG_FILE)
	
 nextpnr-ice40 --force --json $(TOP).json --pcf $(PCF_FILE).pcf --asc $(TOP).asc --freq $(BOARD_FREQ) --$(FPGA_VARIANT) --package $(FPGA_PACKAGE) --opt-timing -q
	
 icetime -p $(PCF_FILE).pcf -P $(FPGA_PACKAGE) -r $(TOP).timings -d $(FPGA_VARIANT) -t $(TOP).asc
	
 icepack -s $(TOP).asc $(TOP).bin

flash:

 iceprog $(TOP).bin

clean:

 rm -rf $(TOP).blif $(TOP).asc $(TOP).bin $(TOP).json $(TOP).timings

terminal:

 sudo picocom -b $(BAUDS) $(PICO_DEVICE) --imap lfcrlf,crcrlf --omap delbs,crlf --send-cmd "ascii-xfr -s -l 30 -n"

cycle:

 git pull

 make
	
 sudo make flash

## TO NOTE:-

1. This Makefile automates the VSDSquadron_FM FPGA project.

2. It defines key variables like top module, pin constraints, and clock frequencies.

3. Clean removes build files. terminal opens a serial port with picocom.

4. The cycle target updates the project, rebuilds, and reflashes.

5. This simplifies the development workflow by automating repetitive tasks.

