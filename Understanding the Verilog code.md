## UART TRANSMITTER MODULE IN VERILOG

- Let's begin with a basic UART Transmitter module in Verilog.

- This module will take a data byte as input and transmit it serially. We'll need to define parameters for the baud rate and clock frequency to calculate the number of clock cycles per bit.

module uart_transmitter #(
 
    parameter CLK_FREQUENCY = 50_000_000, // Example: 50 MHz clock
    
    parameter BAUD_RATE       = 9600          // Example: 9600 bps

) (

    input wire clk,
    
    input wire rst_n,
    
    input wire [7:0] tx_data,
    
    input wire tx_en,
    
    output reg tx_out,
    
    output reg tx_busy

);

    // Calculate clock cycles per bit
    localparam CLOCKS_PER_BIT = CLK_FREQUENCY / BAUD_RATE;

    // Internal state machine states
    typedef enum logic [1:0] {
        IDLE,
        START_BIT,
        DATA_BITS,
        STOP_BIT
    } tx_state_t;

    reg tx_state, next_tx_state;
    reg [2:0] bit_index;
    reg [31:0] bit_counter;
    reg [7:0] tx_data_reg;

    // State register
    always_ff @(posedge clk or negedge rst_n) begin
        if (!rst_n) begin
            tx_state <= IDLE;
            tx_busy <= 1'b0;
        end else begin
            tx_state <= next_tx_state;
            if (next_tx_state == IDLE) begin
                tx_busy <= 1'b0;
            end
        end
    end

    // Next state logic
    always_comb begin
        next_tx_state = tx_state;
        case (tx_state)
            IDLE:
                if (tx_en) begin
                    next_tx_state = START_BIT;
                end
            START_BIT:
                next_tx_state = DATA_BITS;
            DATA_BITS:
                if (bit_index == 3'd7) begin
                    next_tx_state = STOP_BIT;
                end
            STOP_BIT:
                next_tx_state = IDLE;
            default:
                next_tx_state = IDLE;
        endcase
    end

    // Output logic and counters
    always_ff @(posedge clk or negedge rst_n) begin
        if (!rst_n) begin
            tx_out <= 1'b1; // Idle state is high
            bit_counter <= 32'd0;
            bit_index <= 3'd0;
            tx_data_reg <= 8'd0;
        end else begin
            if (tx_state == IDLE && tx_en) begin
                tx_data_reg <= tx_data;
                tx_busy <= 1'b1;
                bit_counter <= 32'd0;
                bit_index <= 3'd0;
            end else if (tx_state == START_BIT) begin
                if (bit_counter < CLOCKS_PER_BIT - 1) begin
                    bit_counter <= bit_counter + 1'b1;
                    tx_out <= 1'b0; // Start bit is low
                end else begin
                    bit_counter <= 32'd0;
                end
            end else if (tx_state == DATA_BITS) begin
                if (bit_counter < CLOCKS_PER_BIT - 1) begin
                    bit_counter <= bit_counter + 1'b1;
                    tx_out <= tx_data_reg[bit_index];
                end else begin
                    bit_counter <= 32'd0;
                    bit_index <= bit_index + 1'b1;
                end
            end else if (tx_state == STOP_BIT) begin
                if (bit_counter < CLOCKS_PER_BIT - 1) begin
                    bit_counter <= bit_counter + 1'b1;
                    tx_out <= 1'b1; // Stop bit is high
                end
            end
        end
    end

endmodule
  
## Explanation:

**Parameters:** CLK_FREQUENCY and BAUD_RATE need to be set according to your FPGA clock and desired UART speed.

### Inputs:

**clk:** System clock.

**rst_n:** Asynchronous reset (active low).

**tx_data:** 8-bit data to be transmitted.

**tx_en:** Enable signal to start transmission.

### Outputs:

**tx_out:** Serial transmit data line.

**tx_busy:** Indicates when the transmitter is busy sending data.

**CLOCKS_PER_BIT:** Calculated based on the clock frequency and baud rate.

**State Machine:** Controls the transmission sequence (IDLE, START_BIT, DATA_BITS, STOP_BIT).

**Counters:** bit_counter tracks the number of clock cycles within a bit period, and bit_index tracks the current data bit being transmitted.

**tx_data_reg:** Stores the data to be transmitted.

### Transmission Sequence:

- When tx_en is asserted in the IDLE state, the state machine moves to START_BIT.

- A start bit (logic low) is transmitted for CLOCKS_PER_BIT.

- The 8 data bits from tx_data_reg are transmitted sequentially, starting from the least significant bit. Each bit is held for CLOCKS_PER_BIT.

- A stop bit (logic high) is transmitted for CLOCKS_PER_BIT.

- The state machine returns to IDLE.

- **tx_busy:** Goes high when transmission starts and low when it finishes.

## MODULE SIMPLE SENSOR INTERFACE

module simple_sensor_interface (
    
    input wire clk,
    
    input wire rst_n,
    
    input wire sensor_data_ready, // Signal from sensor indicating data is valid
    
    input wire [7:0] sensor_data_out, // 8-bit data from the sensor
    
    output reg [7:0] processed_sensor_data,
    
    output reg sensor_data_valid // Indicates when processed data is available

);

    reg [7:0] internal_sensor_data;
    reg data_captured;

    always_ff @(posedge clk or negedge rst_n) begin
        if (!rst_n) begin
            internal_sensor_data <= 8'd0;
            processed_sensor_data <= 8'd0;
            data_captured <= 1'b0;
            sensor_data_valid <= 1'b0;
        end else begin
            if (sensor_data_ready && !data_captured) begin
                internal_sensor_data <= sensor_data_out;
                data_captured <= 1'b1;
                sensor_data_valid <= 1'b1; // Indicate new data is available
            end else begin
                sensor_data_valid <= 1'b0; // Reset valid signal after some time or when processed
            end

            // For this basic example, we'll just pass the data through
            if (data_captured) begin
                processed_sensor_data <= internal_sensor_data;
                data_captured <= 1'b0; // Reset for the next reading
            end
        end
    end

endmodule

## Explanation:

### Inputs:

**clk:** System clock.

**rst_n:** Asynchronous reset (active low).

**sensor_data_ready:** A signal from your hypothetical sensor that goes high when new data is available on sensor_data_out.

**sensor_data_out:** The 8-bit data output from your sensor.

### Outputs:

**processed_sensor_data:** In this basic example, it's just the raw sensor data being passed through. In a real system, this is where you would connect your data processing unit.

**sensor_data_valid:** A signal that goes high for one clock cycle when new sensor data has been captured and is available in processed_sensor_data.

### Internal Signals:

**internal_sensor_data:** A register to hold the captured sensor data.

**data_captured**: A flag to indicate that new data has been read from the sensor.

## BASIC DATA PROCESSING UNITS

module top_module (
    
    input wire clk,
    
    input wire rst_n,
    
    input wire [7:0] input_a,
    
    input wire [7:0] input_b,
    
    output wire [8:0] result_sum

);

    // Instantiate the basic_adder module
    basic_adder adder_instance (
        .clk(clk),
        .rst_n(rst_n),
        .data_in_a(input_a),
        .data_in_b(input_b),
        .sum(result_sum)
    );

    // ... other logic in your top-level module ...

endmodule

## Explanation

### Module Declaration:

**module basic_adder (...):** Defines a Verilog module named basic_adder.

### Inputs:

**clk:** Clock signal for synchronous operation.

**rst_n:** Asynchronous reset signal (active low).

**data_in_a [7:0]:** 8-bit input for the first operand.

**data_in_b [7:0]:** 8-bit input for the second operand.

### Output:

**sum [8:0]:** 9-bit output register to store the sum of data_in_a and data_in_b. It's 9 bits wide because the sum of two 8-bit numbers can require up to 9 bits (e.g., 255 + 255 = 510).

### Sequential Logic (always_ff block):

**always_ff @(posedge clk or negedge rst_n) begin ... end:** This is a synchronous always block, meaning its operations are synchronized with the rising edge of the clock signal 

**(clk).** It also includes an asynchronous reset.

### Reset Condition (if (!rst_n) begin ... end):

- If the reset signal rst_n is low (active low reset), **the sum register is reset to 0**. This initializes the output when the system starts or is reset.

### Addition Operation (else begin ... end):

- In the normal operation (when rst_n is high), **the sum register is assigned the result of adding data_in_a and data_in_b**. This addition is performed on every rising edge of the clock.
