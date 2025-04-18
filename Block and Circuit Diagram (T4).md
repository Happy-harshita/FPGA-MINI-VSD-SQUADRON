## BLOCK DIAGRAM

    +-----------------------+                     +-----------------------+

    | External Clock (12 MHz)|                ---->|FPGA (iCE40 UP5K)   |

    +-----------------------+                      +-----------------------+
                               
                                       ^
                                    
                                       | hw_clk (Pin 20)
                                       
                                       |
                               +-------+-------+
                             
                               |               |
                   
                    +----------v-----+     +----v-----+
                    
                    | Internal        |     | Clock     |
                    
                    | Oscillator (~48 |---->| Divider   |
                    
                    | MHz)            |     | (/4)      |
                    
                    +-----------------+     +----------+
                    
                                       ^          ^
                                       
                                       |          | ~12 MHz
                                       
                                       |          |
                    +------------------           ---------------------
                    
                    +                                                 +
                  
                    |                                                 |
          +---------v---------+                                         +---------v---------+
         
          | 9600 Hz Clock     |                                         | 28-bit Counter    |
          
          | Generator         |              <------------              | (~12 MHz clock) |
          
          +-------------------+                                         +-------------------+
          
                    ^                                           ^
                    
                    | clk_9600                                  | frequency_counter_i[24] (senddata)
                    
                    |                                           |
          +---------
          
          v---------+---------                                                  +---------v---------+
         
          | UART Transmitter  |       --------------------------->               | RGB LED Driver  |
          
          | (8N1)             |             txbyte ("D")                         |                 |
          
          +-------------------+                                                 +-------------------+
                    ^                                           ^     ^     ^
                    |                                           |     |     |
          +---------v---------+                           +-----v-----+-----v-----+
       
          | UART TX (Pin 14)  |                           | RGB LED (Pins 39, 40, 41) |
          
          +-------------------+                           +---------------------------+
                                       ^
                                  
                                       | uartrx (Pin 15)
                                       
                                       |
         
          +-------------------+
          
          | UART RX (Pin 15)  |         ----------------------> (Connected to RGB Driver PWM inputs)
          
          +-------------------+

## CIRCUIT DIAGRAM:-

                                  +-----------------+
                                 
                                  | iCE40 UP5K FPGA |
                                  
                                  |    (SG48)       |
                                  
                                  +--------+--------+
                                         
                                         |
                        
                         +-----------------+-----------------+
                         
                         |                 |                 |
               
                +--------v--------+    +--------v--------+    +--------v--------+
                
                |   LED Red       |    |  LED Green      |    |   LED Blue      |
                
                | (Pin 39)        |    |  (Pin 40)       |    |   (Pin 41)       |
                
                +-----------------+    +-----------------+    +-----------------+
                         
                         |                 |                 |
                
                         |                 |                 |
                         
                         +-----------------+-----------------+
                         
                                         |
                                         
                                         | (Internal connection to RGB Driver in FPGA)
                                         
                                         |
                
                +-----------------+    +-----------------+
                
                | UART TX         |--->| RX of Serial-   |
                
                | (Pin 14)        |    | to-USB Adapter  |
                
                +-----------------+    +-----------------+
                
                         |
                         
                         |
               
                +--------v--------+
                
                | UART RX         |
                
                | (Pin 15)        |---> (Connected internally to RGB Driver PWM inputs)
                
                |-----------------| (Connect TX of Serial-to-USB Adapter if needed for RX)
                         
                         |
                         
                         |
               
                +--------v--------+
                
                | HW_CLK          |<---| External Clock  |
                
                | (Pin 20)        |    | (12 MHz)        |
                
                +-----------------+    +-----------------+
                
                         |
                         
                         |
               
                +--------v--------+                   -------------------
                
                | Power & Ground  |     <---         | Power Supply    |
                
                | (Multiple Pins) |                   +-----------------+
                
                +-----------------+
