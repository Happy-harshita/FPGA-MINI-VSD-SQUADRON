# FPGA MINI VSD SQUADRON
## PERFORMER DETAILS:-
**NAME:-** HARSHITA

**CLASS:-** 7A

**SCHOOL:-** ARMY PUBLIC SCHOOL, CHENNAI

In this course, I will be performing some tasks and assignments that will be given. Before starting of with the assignments i would like to summarise about the course and the FPGA board.

 FPGA:– Powered by the Lattice UltraPlus ICE40UP5K FPGA– Offers 5.3K LUTs, 1Mb SPRAM, 120Kb DPRAM, and 8 multipliers for versatile design
 
 capabilities
 
**Connectivity** :– Equipped with an FTDI FT232H USB to SPI device for seamless communication– All FTDI pins are accessible through test points for easy debugging and customization
 
**General Purpose I/O (GPIO)** :– All 32 FPGA GPIOs brought out for easy prototyping and interfacing
 
**Memory** :– Integrated 4MB SPI flash for data storage and configuration
 
**LED Indicators** :– RGB LED included for status indication or user-defined functionality
 
**Form Factor** :– Compact design with all pins accessible, perfect for fast prototyping and embedded ap
plications

The VSDSquadron FPGA Mini (FM) board is an affordable, compact tool for prototyping and embedded system development. With powerful ICE40UP5K FPGA, onboard programming, versatile. GPIO access, SPI flash, and integrated power regulation, it enables efficient design, testing, and deployment, making it ideal for developers, hobbyists, and educators exploring FPGA applications.

![image](https://github.com/user-attachments/assets/444164c0-e998-4c5f-97dd-715ea4135f22)

(IMAGE SOURCE - DATASHEET)

## ASSIGNMENT :- 

### PROCEDURE:-

1. Install all the required applications - FPGA mini ZIP Folder, VirtualBox, C++ 2019 redistrubutable, VirtualBox Extension Pack 
   (if required).

2. Add the necessary data - Name:fpga, Folder: D:, ISO Image: <not selected>,Type:Linux, Version: Xubuntu (64-bit)

3. On the next screen, allocate memory as 4GB (or 4096 MB) and number of CPUs as 4.

4. Create a virtual hard disk. Choose the ”Use an existing virtual hard disk file” option and click on the folder icon to browse 
   to the location of the unzipped VDI file on your Windows computer.

5.  Then, click FINISH when you see the summary and your virtual box disk is created.

6.  Click START and the screen is shown.

7.  Then put the password (vsdiat)

8.  Open new terminal.

9.  Let’s start our first project. To navigate through project directories in a UNIX environment,
    use the following commands:
cd
cd VSDSquadron_FM
cd blink_led
The commands above allow you to:– Change to the home directory (‘cd‘).– Navigate to the ‘VSDSquadron FM‘ folder, which has a sample project.– Move into the ‘blink led‘ directory, which is the first FPGA project to be tried on VSD Squadron FPGA Mini (FM) board.

10. There is a preloaded project in ”blink led” directory. To test the project on VSDSquadron
    FPGA Mini (FM) board, we need to make sure that the board is connected to the Oracle Virtual Machine.

11. Toconfirmiftheboard is connected to the USB, type the ‘lsusb‘ command in the terminal. You should see a line stating ”Future 
    Technology Devices International". Run the following command to clean up previous builds.
    - Make clean
    - Make build
    - Sudo make flash

12. Once the code is successfully flashed, you will see the RGB lights on the FPGA board
    blinking.

## RESULT :-

https://github.com/user-attachments/assets/06edc7ef-a624-407c-93d4-d0e5928f8cc5

(IMG SOURCE :- SELF)

------------------------------------------------------------- THE END ------------------------------------------------------------

