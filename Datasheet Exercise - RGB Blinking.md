# ASSIGNMENT :- 

## PROCEDURE:-

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
The commands above allow you to:– Change to the home directory (‘cd‘).– Navigate to the ‘VSDSquadron FM‘ folder, which has a sample 
project.– Move into the ‘blink led‘ directory, which is the first FPGA project to be tried on VSD Squadron FPGA Mini (FM) board.

10. There is a preloaded project in ”blink led” directory. To test the project on VSDSquadron
    FPGA Mini (FM) board, we need to make sure that the board is connected to the Oracle Virtual Machine.

11. Toconfirmiftheboard is connected to the USB, type the ‘lsusb‘ command in the terminal. You should see a line stating "Future 
    Technology Devices International". Run the following command to clean up previous builds.
    - Make clean
    - Make build
    - Sudo make flash
12. Once the code is successfully flashed, you will see the RGB lights on the FPGA board
    blinking.

## RESULT :-

https://github.com/user-attachments/assets/06edc7ef-a624-407c-93d4-d0e5928f8cc5

(IMG SOURCE :- SELF)
