## TASK 3 IMPLEMENTATION:-

1. To navigate through project directories in a UNIX environment, use the following commands: cd cd VSDSquadron_FM cd blink_led The commands above allow you to:– Change to the home directory (‘cd‘).

2. Navigate to the **‘VSDSquadron FM'** folder, which has a sample project.

3.  Move into the **‘blink led‘** directory, which is the first FPGA project to be tried on VSD Squadron FPGA Mini (FM) board.

4. To confirm if the board is connected to the USB, type the **‘lsusb‘** command in the terminal.

5. Run the following commands:-

    - Make clean
    - Make build
    - Sudo make flash (Give password if required)

6. Once the code is successfully flashed, you will see the RGB lights on the FPGA board blinking.

https://github.com/user-attachments/assets/06edc7ef-a624-407c-93d4-d0e5928f8cc5

**(VID SOURCE :- SELF)**

7. Open a terminal within your Virtual Machine.

8. Update the package list: sudo apt-get update

9. Install Minicom: sudo apt-get install minicom. 

10. Set the "Serial Device" to the correct COM port (/dev/ttyUSB0).

11. Save the configuration as "dfl" (default).

12. Exit Minicom configuration.

13. Open Minicom: sudo minicom

14. Minicom will open, and you should see a blank terminal screen.

15. Open a terminal within your Virtual Machine.

16.Update the package list: sudo apt-get update

17. Install PuTTY: sudo apt-get install putty.

18. Execute the command putty.

19. This will cause the PuTTY configuration window to open.

20. In the "Serial line" field, enter the correct COM port (e.g., /dev/ttyUSB0).

21. In the "Speed" field, enter the baud rate.

22. Go to the "Serial" category on the left-hand side of the window.

23. Click the "Open" button. A new terminal window will open.

## TO NOTE:-

- If your FPGA is transmitting data, you should see it displayed in the PuTTY terminal window.

- Always refer to the VSDSquadron FM board's documentation and the datasheet for specific instructions and specifications.

- Troubleshooting: If you encounter errors, carefully read the error messages and refer to the documentation or online resources for help.
