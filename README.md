## Tool

### Hardware
- ESP-01 or ESP-01S
- Arduino Mega 2560 or other Arduino boards
- Power Supply (Stable current is provided to rule out some of the causes of errors)

### Serial Port Monitor
- ESPlorer
- Realterm

### Firmware burning tool
- flash_download_tool_3.9.4

## ESP-01 Firmware burning process

### Connection
- ESP-01 Vcc -> Power Supply 3.3V
- ESP-01 CH_PD -> Power Supply 3.3V
- ESP-01 GND -> GND (Power Supply GND & Arduino GND)
- ESP-01 RX -> Arduino RX0
- ESP-01 TX -> Arduino TX0
- ESP-01 GPIO0 -> GND (Only the firmware should be grounded)

### Let's take a look at the pre-burning message of the firmware
**！ GPIO0 is not grounded at this point**
- ESPlorer
    1. Open ESPlorer.bat
    2. Select the current Arduino Port at the top right of the screen
    3. After confirming the Baud Rate, click Open (usually the default is 115200 or 9600), if only the following message is used, the Port may be selected incorrectly
        ```
        Communication with MCU...
        ```
        At this time, the command 'AT+GMR' has been automatically entered, which is normally displayed as shown in the figure below, listing the firmware AT version, SDK version, and company name (not necessarily)

        ![image](./preview/p1.png)

- Realterm
   1. Open Realterm
   2. Select Ansi-VT100 under Display as on the left side of the Display tab.
   3. Click the Port tab, set the Baud to 115200, pull down the Port menu to select the current Arduino Port, and observe that the corresponding Port and Baud Rate are displayed in the lower right corner of the program (if you want to switch Baud, you need to click the Change button)
   4. Switch to the Send tab, check +CR and +LF on the right side of the first line of text input box, enter `AT` and click as ASCII to send. If OK is not displayed, it means the Baud or Port selection is wrong.
   5. Enter the command `AT+GMR` to observe the firmware details

        ![image](./preview/p2.png)

### Burn ESP-01 firmware
1. Open flash_download_tool_3.9.4.exe
2. Confirm the following options and press OK
     - ChipType: Esp8266
     - WorkMode: Develop
3. Under the SPIDownload tab, click the first... button in the upper half to load the firmware and select BAT_AT_V1.7.1.0_1M.bin
4. The @ field on the right is the starting position of firmware burning, enter 0x0
5. At this time, uncheck the loaded firmware, confirm that GPIO0 of ESP-01 is grounded and confirm the following options, then click START
    - SPISPEED: 40Mz
    - SPI MODE: DOUT
- COM: Corresponding Arduino Port
6. If "Uploading stub..." does not appear immediately in CMD at this time, but "..." is displayed and the green box displays "Waiting for power-on synchronization", restart the Output button of Power Supply and press high Chance can start action
7. At this time, the relevant message has appeared in DetectedInfo, and you can start burning the firmware.
    ![image](./preview/p3.png)

8. Check the previously loaded firmware and press START

     Burning completed
    ![image](./preview/p4.png)

    If an error message occurs during the burning process, usually the firmware has not been burned in and the original firmware of the ESP-01 will not be affected. You can observe it through the above-mentioned Serial Port Monitor and Google for other error messages. According to my experience, if you can't find a solution, you can wait for a period of time (ranging from a few minutes to a day), and it may suddenly get better.

9. Observe the firmware burning status through Serial Port Monitor

     1. Float GPIO0
     2. !!! Power off and then power on ESP-01 !!!
     3. Open any Serial Port Monitor
     4. Enter the command `AT+GMR`
     5. Enter the command `AT+CWMODE=1`

        ![image](./preview/p5.png)

     If ESP-01 is not powered on again, the Serial Port Monitor will become unresponsive.

10. Test whether WiFiEspAT.h can be used under this firmware

     1. Open the PlatformIO project named esp_checkFirmware through vscode
     2. Confirm that you are under esp_checkFirmware Environment
        ![image](./preview/p6.png)

     3. Wiring
        - ESP-01 RX -> Arduino Mega TX1（Hardware Serial 1）
        - ESP-01 TX -> Arduino Mega RX1（Hardware Serial 1）

     Only Arduino Mega has HardwareSerial. If you use other Arduino boards, change main.cpp under /src in the esp_checkFirmware project, use SoftwareSerial and set the baud rate to 9600 (SoftwareSerial cannot use a baud rate that is too fast), and use AT commands at 
     the same time. Set the baud rate of ESP-01 to 9600. In addition, Arduino Mega only has the following Pins that can be used as SoftwareSerial RX: 10, 11, 12, 13, 14, 15, 50, 51, 52, 53, A8 (62), A9 (63), A10 (64), A11 (65), A12 (66), A13 (67), A14 (68), A15 (69)

    4. After uploading the code to Arduino, open the Serial Monitor. The expected result is as shown below.

        ![image](./preview/p7.png)

## Reference

1. [ESP8266 series module topic](https://docs.ai-thinker.com/en/esp8266)
2. [ESP8266 SDK released](https://docs.ai-thinker.com/en/esp8266/sdk)
3. [Flash Download Tools | Espressif Systems](https://www.espressif.com/en/support/download/other-tools)
4. [How to burn firmware for ESP series modules](https://docs.ai-thinker.com/esp_download)
5. [ESP-01S ESP8266 - Throwing Stone Encyclopedia](https://wiki.diustou.com/cn/ESP-01S_ESP8266)
