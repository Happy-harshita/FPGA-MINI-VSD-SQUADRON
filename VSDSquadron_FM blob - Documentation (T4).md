## CODE:-
TOP=top

PCF_FILE=VSDSquadronFM

BOARD_FREQ=12

CPU_FREQ=20

FPGA_VARIANT=up5k

FPGA_PACKAGE=sg48

VERILOG_FILE=top.v


#Uart Var

PICO_DEVICE=/dev/ttyUSB0   # replace by the terminal used by your device

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

## Top-level module: 
Top (defined by TOP=top)

## Verilog source file:
Top.v (defined by VERILOG_FILE=top.v)

## FPGA target:
Lattice iCE40 UP5K (defined by FPGA_VARIANT=up5k and FPGA_PACKAGE=sg48)

## Clock frequency:
The Makefile mentions BOARD_FREQ=12 (likely 12 MHz for the board oscillator) and CPU_FREQ=20 (which might be a target frequency for internal logic, but the actual clock 
driving the UART will depend on the design in top.v).

## UART settings:
The terminal target shows the baud rate is set to 9600 (BAUDS=9600) and the Pico device is /dev/ttyUSB0.

## PCF:-

**set_io  led_green 40**

**set_io  led_red 39**

**set_io  led_blue 41**

**set_io  uarttx 14**

**set_io  uartrx 15**

**set_io  hw_clk 20**

- led_green is assigned to pin 40.

- led_red is assigned to pin 39.

- led_blue is assigned to pin 41. 

- These likely correspond to LEDs on the development board that the design might control for status indication.

- uarttx (UART Transmit) is assigned to pin 14. This is the crucial output pin that will send the serial data.

- uartrx (UART Receive) is assigned to pin 15.

- This is the input pin for receiving serial data, although it might not be used if the uart_tx_sense project is purely for transmitting sensor data.

- hw_clk (Hardware Clock) is assigned to pin 20. 

- This suggests that the clock signal for the FPGA logic is likely coming from an external oscillator connected to this pin on the board.

- The BOARD_FREQ=12 in the Makefile likely refers to the frequency of this external clock.

## TOP.V

include "uart_trx.v"



//----------------------------------------------------------------------------

//                                                                          --

//                         Module Declaration                               --

//                                                                          --

//----------------------------------------------------------------------------

module top (

  // outputs

  output wire led_red  , // Red

  output wire led_blue , // Blue

  output wire led_green , // Green

  output wire uarttx , // UART Transmission pin

  input wire uartrx , // UART Transmission pin

  input wire  hw_clk

);



  wire        int_osc            ;

  reg  [27:0] frequency_counter_i;

  

/* 9600 Hz clock generation (from 12 MHz) */

    reg clk_9600 = 0;

    reg [31:0] cntr_9600 = 32'b0;

    parameter period_9600 = 625;

    

uart_tx_8n1 DanUART (.clk (clk_9600), .txbyte("D"), .senddata(frequency_counter_i[24]), .tx(uarttx));

//----------------------------------------------------------------------------

//                                                                          --

//                       Internal Oscillator                                --

//                                                                          --

//----------------------------------------------------------------------------

  SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));





//----------------------------------------------------------------------------

//                                                                          --

//                       Counter                                            --

//                                                                          --

//----------------------------------------------------------------------------

  always @(posedge int_osc) begin

    frequency_counter_i <= frequency_counter_i + 1'b1;

        /* generate 9600 Hz clock */

        cntr_9600 <= cntr_9600 + 1;

        if (cntr_9600 == period_9600) begin

            clk_9600 <= ~clk_9600;

            cntr_9600 <= 32'b0;

        end

  end



//----------------------------------------------------------------------------

//                                                                          --

//                       Instantiate RGB primitive                          --

//                                                                          --

//----------------------------------------------------------------------------

  SB_RGBA_DRV RGB_DRIVER (

    .RGBLEDEN(1'b1                                            ),

    .RGB0PWM (uartrx),

    .RGB1PWM (uartrx),

    .RGB2PWM (uartrx),

    .CURREN  (1'b1                                            ),

    .RGB0    (led_green                                       ), //Actual Hardware connection

    .RGB1    (led_blue                                        ),

    .RGB2    (led_red                                         )

  );

  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";

  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";

  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";



endmodule

- The line include "uart_trx.v" indicates that the UART transmitter logic is likely defined in a separate file named uart_trx.v. 

- We will need the content of this file to fully understand the UART implementation.

- led_red, led_blue, led_green: Output wires connected to LEDs.

- uarttx: Output wire for the UART transmit signal.

- uartrx: Input wire for the UART receive signal (currently not directly used for transmission in this top module).

- hw_clk: Input wire for an external hardware clock.

- ire int_osc;:

- Declares a wire to carry the internal oscillator signal.

- SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));:

- This instantiates a high-frequency internal oscillator primitive (SB_HFOSC) available on iCE40 FPGAs. 

- The CLKHF_DIV ("0b10") parameter likely divides the internal oscillator frequency (typically around 48 MHz) by 4, resulting in a clock frequency of approximately 12 MHz. 

- This int_osc signal is used as the main clock for the counter.

- reg clk_9600 = 0;: A register to generate the 9600 Hz clock for the UART.
  
- reg [31:0] cntr_9600 = 32'b0;: A 32-bit counter to divide the int_osc frequency.

- Parameter period_9600 = 625;:

- This parameter determines the division factor to achieve approximately 9600 Hz from the int_osc (assuming int_osc is 12 MHz, 12,000,000 / 9600 / 2 = 625).

- The always @(posedge int_osc) block increments the cntr_9600 and toggles clk_9600 when cntr_9600 reaches period_9600, effectively generating a 9600 Hz clock signal.

- .clk (clk_9600): The UART transmitter is clocked by the generated 9600 Hz clock.

- .txbyte("D"): This suggests that the UART transmitter is configured to transmit the ASCII character 'D' (represented by its 8-bit binary value).

- .senddata(frequency_counter_i[24]): This is interesting. It seems the transmission of 'D' is triggered by the 25th bit of the frequency_counter_i.

- This likely means 'D' is transmitted when this bit transitions from 0 to 1 (or vice versa, depending on the uart_tx_8n1 implementation).

- This counter is running at the int_osc frequency.

- .tx(uarttx): The output of the UART transmitter is connected to the uarttx pin of the top module.

- The code instantiates an SB_RGBA_DRV primitive to control an RGB LED.

- .RGB0 (led_green), .RGB1 (led_blue), .RGB2 (led_red): Connect the outputs of the RGB driver to the LED pins.

// 8N1 UART Module, transmit only

## UART TX

module uart_tx_8n1 (

    clk,        // input clock

    txbyte,     // outgoing byte

    senddata,   // trigger tx

    txdone,     // outgoing byte sent

    tx,         // tx wire

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

## Analysis of uart_trx.v:

- This module implements a basic 8N1 (8 data bits, No parity, 1 stop bit) UART transmitter. Let's break down its functionality:

- Ports:

- clk: Input clock signal.

- txbyte: Input 8-bit data byte to be transmitted.

- senddata: Input signal to trigger the transmission of the txbyte. Transmission starts on the rising edge of senddata when the UART is in the STATE_IDLE.

- txdone: Output signal that goes high for one clock cycle after the entire byte (including start and stop bits) has been transmitted.

- tx: Output wire carrying the serial transmit data.

- Parameters:

- STATE_IDLE, STATE_STARTTX, STATE_TXING, STATE_TXDONE: Define the different states of the UART's finite state machine (FSM).

- State Variables:

- state: An 8-bit register to hold the current state of the FSM.

- buf_tx: An 8-bit register to buffer the txbyte that is being transmitted.

- bits_sent: An 8-bit register to keep track of the number of data bits transmitted.

- txbit: A 1-bit register that holds the current bit being transmitted on the tx line.

- txdone: A 1-bit register indicating the completion of transmission.

- Wiring:

- assign tx=txbit;: The tx output pin is directly driven by the txbit register.

- always @ (posedge clk) block: This sequential block describes the behavior of the UART transmitter on each rising edge of the clock:

- Idle State (STATE_IDLE): When senddata goes high and the UART is idle, it transitions to STATE_STARTTX, loads the txbyte into buf_tx, and resets txdone. 

- While idle, the txbit is held high.

- Start Bit Transmission (STATE_STARTTX): The txbit is driven low for one clock cycle to signal the start of transmission, and the state transitions to STATE_TXING.

- Data Bit Transmission (STATE_TXING): This state iterates 8 times (for 8 data bits). In each iteration:

- The least significant bit of buf_tx is driven onto the txbit.

- buf_tx is right-shifted to prepare the next bit for transmission.

- bits_sent is incremented.

- Once 8 bits are sent, the state transitions to STATE_TXDONE.

- Stop Bit Transmission (STATE_TXDONE): The txbit is driven high for one clock cycle to signal the end of transmission.

- bits_sent is reset, txdone is set high for one clock cycle, and the state returns to STATE_IDLE.
