# QCoo-Code

Arduino code for the QCoo cube, a LED cude with 5x5 LEDs on each of the six sides, overall  150 LEDS!


## Hardware parts


* 150x WS2812 NeoPixel LEDs 
* 1x InvenSense MPU-6050 sensor contains a MEMS accelerometer and a MEMS gyro in a single chip
* ...

## Hardware connections

* Pin0
* Pin1
* Pin2
* Pin3
* Pin4
* Pin5 - 75 LEDS, 2nd LED array, controlled via Adafruit NeoPixel lib
* Pin6 - 75 LEDs, 1st LED array, controlled via Adafruit NeoPixel lib
* Pin7
* Pin8
* Pin9

* UART0 - "Serial" UART used to receive and send information


## Serial protocol

0xF0 + 0x55 => signal start of new data
51 is maximal length of data (if more bytes are received, the data is reset and a new signal start must be sent

* header
1 = signal start
2 = data length, defines the number of bytes to read until data is complete
* payload
3 = idx
4 = action
5 = device
6 ... = more data depending of the first three payload bytes



## Wireless upgrade (google translated from *.docx file) 

Wireless upgrade QCOO hardware method:
1. Search for QCOO_ALPHA hardware;
2. Write 0b00100000 to FFF1 of service UUID: 0xFFF0;
3. Write 0b00000000 to FFF1 of service UUID: 0xFFF0;
4. Write 0b00100000 to FFF2 of service UUID: 0xFFF0;
5. At this time QCOO hardware will be forced to reset once;
6. In 100ms to the QUO UUID: FFE5 FFE9 write upgrade file can be

Note: 
1. Check the file can be ignored, do not read back, otherwise the upgrade will fail.
2. Do not recommend air upgrades, it is recommended to use the data cable to update the firmware.


## Development agreement (google translated from Qcoo*.pdf file) 

(page 1)

TOC

|Item Name|Version Number|Remarks Modification|Date      |Modifier|
|---------|--------------|--------------------|----------|--------|
|QCoo     |2.0.2 |Complete all used protocols     |2016.09.01|Liang Yu|
|QCoo     |2.0.3 |modify LED0 error               |2016.09.21|Liang Yu|
|QCoo     |2.0.4 |KS Version: Modified baud rate  |2017.01.03|Liang Yu|

(page 2)

## Description

QCoo hardware is deeply compatible with Arduino, Arduino's processor and Arduino's Bootloader, you can use Arduino's IDE to QCoo
Hardware to rewrite the firmware (secondary secondary development) operation. Note that you can select the UNO board in the Arduino IDE.
On the understanding of QCoo hardware: In fact, QCoo hardware can be abstractly understood as a monitor, this monitor can be through the Bluetooth and intelligent devices
Communication, intelligent equipment can be controlled by the form of QCoo control, the main control LED lights display, including color, brightness, location and other letters
interest. At the same time QCoo hardware is not exactly the implementation of the implementation, he will interact with the phone, because QCoo hardware mount the attitude sensor, then QCoo hard
Will be reported to the intelligence device gesture information, the information through the intelligent device processing, you can know QCoo hardware posture changes, so smart devices
Can be more effective with the QCoo interaction, whenever the smart device to QCoo hardware to send a command, QCoo hardware will tell the host, whether the implementation,
Or whether the implementation of success and other information. This ensures the robustness of the communication.


## QCoo hardware and intelligent devices between the communication is divided into two categories:

One is the smart device sending an execution command to make QCoo execute;
The other is the intelligent device to send a query command to query the current status of QCoo hardware;
The following protocol content will be based on the above two categories, divided into the implementation of class and query class. The customization of the communication protocol determines the communication efficiency and the efficient communication can be guaranteed
The show shows the real-time, experience will be good.

### 1. the implementation of class:

1. Control the LED method 1
2. Control LED method 2
3. Control LED method 3
4. Refresh the display
5. Set the brightness
6. Effect switch
7. Shut down
8. motor vibration
9. Set the connection status
10. Save the brightness
11. Dice mode
12. Initialize the gyroscope


(page 3)


### 2. the query categories:
13. Query the color
14. Query the voltage
15. Check the charging status

## Protocol design format

|Start code 1 |start code 2 |length identification |command ID |action |command word |data|
|-------------|-------------|----------------------|-----------|-------|-------------|----|
|0xf0 |0x55 |Indicates that the command is long Degrees (removal start Bit and length Knowledge itself) |Used to instruct commands Index (for the host Distinguish asynchronous commands) |Identify control instructions and Query instructions 0x02 control 0x01 query |Above the two big Class, one is executive Line, one class is The query |Command word with parameters To assist in the completion of the command word Function|


QCoo hardware has six faces marked as 0x01, 0x02, 0x03, 0x04, 0x05, 0x06;
QCoo hardware has 30 colors exist in the hardware local, numbered 0x00 ~ 0x29;
QCoo hardware set the brightness of the standard is 0 to 255, brightness increased sequentially, expressed in hexadecimal to 0x00 ~ 0xFF;
QCoo hardware for standard serial communication, the default baud rate is 9600;

### 1. control LED method 1: 0x41

|Start code |length identification |command ID |action |command word |color code |lamp number |
|-----------|----------------------|-----------|-------|-------------|-----------|------------|
|0xF0 0x55  | 0x05                 |0x00       |0x02   |0x41         |0x01       |0x7D        |


Send the above data: F05505410241017D to QCoo hardware
1. Effect: The 125th light is on, the color is the color of the color 1, the order of the command word is 0x41, on behalf of "Control LED method 1".
2. Parameters: Data is 0x01 and 0x7D The meaning of the two parameters represent the color of the lamp to be operated and the ID number of the lamp.
3. Return Value: None
4. Note: This command is suitable for one operation of a LED, especially for snake display.

(page 4)

## 2. control LED method 2: 0x11


|Start code |length identification |command ID |action |command word |Face number |color coding |Data 1 |Data 2 |Data 3 |Data 4|
|-----------|----------------------|-----------|-------|-------------|------------|-------------|-------|-------|-------|------|
|0XF0 0x55  |0x09                  |0x00       |0x02   |0x11         |0x01        | 0x01        |0x01 |0x02 |0x02 |0x01|

Send the above data: F05509000211010101020201 to QCoo Hardware:
1. Effect: Face number 0x01 on the surface, some light color will become the color number 1 color. The command word is 0x11, which means "Control LED Method 2".
2. Parameters: data 1 ~ data 4 is used to express the lights on the surface, it can be said that the four data can be any light on the surface of the light off state.
3. Return Value: None
4. Note: The four data on how to obtain, QCoo team designed a special software, used to generate data 1 ~ data 4. three,


### 3. control LED method 3: 0x14

|Start Code |length identification |command ID |action |command word |Face number |color coding |
|-----------|----------------------|-----------|-------|-------------|------------|-------------|
|0XF0 0x55  |0x04                  |0x00       |0x02   |0x15         |0x01        | 0x12        |

Send data F055050002130112 to QCoo hardware:
1. Effect: face marked 0x01 on the face, all the color of the color into the color number 12 color. The command word for the command represents "Control LED Method 3".
2. Parameters: Face number 0x01 represents the first face, the parameter color number 0x12 represents the color of the color number 12.
3. Return Value: None
4. The order is suitable for brushing a separate face, making special effects.


### 4. refresh display: 0x03

|Start code |length identification |command ID |action |command word |
|-----------|----------------------|-----------|-------|-------------|
|0XF0 0x55  |0x03                  |0x00       |0x02   |0x03         |

Send F05503000203 to QCoo hardware:
1. Effect: forced to refresh the current display of the current QCoo.
2. Parameters: The command has no parameters.
3. Return Value: None
4. Note: QCoo received a command will be followed by the implementation of the refresh, this command is rarely used, because each display instructions are issued data refresh function.
Fives,

(page 5)

## 5. set the brightness: 0x20
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x04         |0x20     |

Send F0550400020420 to QCoo hardware:
1. Effect: After receiving the command to get the parameters to set the brightness, and then set the brightness of QCoo hardware, and then refresh the display.
2. Parameters: The global brightness is set to 32 for the corresponding decimal data of parameter 0x20.
3. Return value: F0 55 01 00 10 0D 0A
4. Note: the display will be refreshed in real time, when set the brightness of 0x00 when the QCoo will not accept, the minimum brightness of 0x0A (decimal 10), the purpose is to prevent the wrong set brightness, the subsequent display can not render the effect.

### 6. special effects switch: 0x05

|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x05         |0x01     |

Send F0550400020501 to QCoo hardware:
1. Effect: switch to the special effects mode, and show the effects of special effects 1, the command word 0x05 represents the significance of switching effects.
2. Parameters: parameter 0x01 represents the switch to the effects, the hardware set up six effects, but the actual use of the four effects
It is worth noting that the effect is off when the parameter is 0x00
3. Return value: F0 55 01 00 10 0D 0A
4. Note: When you are ready to jump out from the effects inside to remember to turn off the effects, and then send other commands to make sense. The command to turn off effects is
F0550400020500.

### 7. shut down: 0x15
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x05         |0x0A     |

Send F055040002050A to QCoo hardware:
1. Effect: After receiving the command, immediately shut down, no return value.
2. Parameters: parameter 0x0A no practical significance, the purpose is to prevent the analysis is wrong, false shutdown.
3. Return value: F0 55 01 00 10 0D 0A
4. Note: After all the operation is invalid, you need to shake and shake to start QCoo.

(page 6)

### 8. motor vibration: 0x16
|Start code |length identification |command ID |action |command word |vibration intensity|vibration time|
|-----------|----------------------|-----------|-------|-------------|-------------------|--------------|
|0xF0 0x55  |0x05                  |0x00       |0x02   |0x16         |0x7D               |0xFF          |

Send F055050002167DFF to QCoo Hardware:
1. Effect: After receiving the instruction the motor starts to shake, the intensity of vibration is 0x7D, the duration of vibration is 0xFF;
2. Parameters: vibration intensity from 0x00 ~ 0xFF, vibration increased in turn, vibration duration from 0x00 ~ 0xFF followed by growth.
3. Return value: F0 55 01 00 10 0D 0A
4. Note: motor vibration for the independent behavior, does not affect the implementation of other instructions

### 9. set the connection status: 0x20
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x20        |0x01     |

Send F0550400022001 to QCoo hardware
1. Effect: QCoo will enter the working state after receiving this command.
2. Parameters: The meaning of parameter 0x01 is that the APP is actively connected to the QCoo hardware. The meaning of parameter 0x00 is that the APP is actively disconnected from QCoo
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: Only after the success of all the commands are meaningful, in the new version of the firmware can also skip the connection, send the command directly. When APP is sent
F0550400022000 to QCoo, if three minutes did not connect QCoo and did not move QCoo, QCoo hardware will automatically shut down, lower Power consumption.

(page 7)

### 10. save the brightness: 0x45
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x45         |0x3c     |

Send F055040002453C to QCoo hardware:
1. Effect: QCoo no obvious effect, but the current parameters of the brightness stored in the EEPROM inside, after the restart the brightness information will not be lost.
2. Parameters: The parameter stores the information of the brightness, from 0x00 to 0xFF (the actual effect starts at 0x0A)
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: This command stores the information in the EEPROM, each time the power-on QCoo hardware goes back to read the brightness information as the initialization brightness.

### 11. dice mode: 0x42
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x42         |0x01     |

Send F0550400024201 to QCoo hardware:
1. Effect: QCoo hardware will enter the dice mode, pick up QCoo can be used when the dice.
2. Parameters: There are two parameters of the command, 0x01 on behalf of the dice into the command, 0x00 on behalf of the exit dice command.
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: This mode can trigger QCoo built-in dice mode, exit the need to send F0550400024201 to QCoo hardware.

### 12. initialize the gyroscope: 0x43
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x43         |0x01     |

Send F0550400024301 to QCoo Hardware:
1. Effect: QCoo will automatically initialize the attitude sensor once.
2. Parameters: 0x01 does not make sense, just to prevent false triggering.
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: Sometimes the IIC bus will break with the attitude sensor, if found this situation, then send the command will resume communication.

(page 8)

### 13. query voltage: 0x17
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x17         |0x0A     |

Send F055040002170A to QCoo hardware:
1. Effect: no practical effect, in the APP side will receive the Bluetooth Notify message, the contents of the current QCoo voltage, the form of a string
2. Parameters: 0x0A no practical significance, just to prevent false triggering
3. Return value: voltage value, for example: 4.2; this return value for the string mode
4. Note: This voltage value can roughly reflect the battery power status, receive attention is the string type.

### 14. query charging status: 0x18
|Start code |length identification |command ID |action |command word |parameter|
|-----------|----------------------|-----------|-------|-------------|---------|
|0xF0 0x55  |0x04                  |0x00       |0x02   |0x18         |0x0A     |

Send F055040002180A to QCoo Hardware:
1. Effect: no practical effect, in the APP side will receive the Bluetooth Notify message, the contents of the current QCoo charging state, the form of a string.
2. Parameters: 0x0A no practical significance, just to prevent false triggering.
3. Return value: The charge status value, "0" means no charge, "1" represents charge.
4. Note: APP did not use the directive, only for the developer reference, with the voltage query, then you can predict the battery charge status (saturated or in charge)