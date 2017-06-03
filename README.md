# QCoo

QCoo, the first high-tech gadget merges classic game Snake into 3D space, with AR, LED dice and LED effect.

Shake your QCoo, automatically pair with your smartphone (both Andriod and iOS), BANG! You are ready to go!

QCoo is the world’s first high-tech gadget that puts classical games into 3D space and merges new tech with old game, using Bluetooth connectivity to bring you a portable game solution (6oz) that lightens your day.

It is 3D games and AR game that make QCoo so special. At the moment, QCoo has two 3D games, Snake and Q Run. 
And one AR game, which is AR Tower. We would like to put one more AR fighting game, Home defender: Armour fighters, into QCoo.

Programmable, Compatible with Arduino. 
QCoo is programmable via Bluetooth.

Wireless charging, 1.5 hours. 1500 mAh battery, last for 10 hours.

Arduino code for the QCoo cube, a LED cude with 5x5 LEDs on each of the six sides, overall  150 LEDS!



# QCoo Protocol 

(Imporoved Protocol documentation based on QCoo Protocol.docx and the google translated PDF file) 

## Description

QCoo hardware is Arduino compatible, and it has the same bootloader as Arduino UNO. You can use Arduino IDE update your firmware.

What’s QCoo  : 
QCoo is a display device which can community via BLE with smart phones. 
The smart phone can send commands to QCoo to control leds brightness, color, position etc.
QCoo is not only a receiver of commands, QCoo will send the gyro data to smart phones via BLE for gesture information and position changes. 
Every time a smart phone sends a command to QCoo, QCoo  will send back an OK message, if QCoo has executed the command successfully. This ensures a robust communication.


## The Commands and Queries actions

The *Commands* is communitions of a smart device sending a command to QCoo for execution.
The other is the *Query* of the current QCoo hardware status by the smart device.

The following protocol defines the supported commands and queries:

### 1. Commands

1. Control LED method 1
2. Control LED method 2
3. Control LED method 3
4. Refresh display
5. Set brightness
6. Change effects
7. Power off
8. Motor shake
9. Set connection status
10. Save brightness
11. Dice mode
12. Initialize gyroscope

### 2. Queries

13. Query color
14. Query voltage
15. Query charging status


## Protocol data format

|Start code 1 |start code 2 |length                           | Cmd ID   | action                    | Command        | Data               |
|-------------|-------------|---------------------------------|----------|---------------------------|----------------|--------------------|
|0xf0         |0x55         |byte length of this transmission | Not used | 0x02 control / 0x01 query | Command number | Command parameters |


QCoo hardware has six faces marked as 0x01, 0x02, 0x03, 0x04, 0x05, 0x06;
QCoo has 30 colors pallet, stored in local Arduino ROM memory, numbered 0x00 - 0x29;
QCoo has 256 brightness levels between 0x00 - 0xFF, brightness values less than 10 marked as 10
QCoo has a hardware serial port and communication baud rate is 9600


### 1. control LED method 1: 0x41

|Start code |length |Cmd ID |action |command |color index |LED number |
|-----------|-------|-------|-------|--------|------------|-----------|
|0xF0 0x55  | 0x05  |0x00   |0x02   |0x41    |0x01        |0x7D       |


Send ``F05505410241017D`` to QCoo hardware
1. Effect: The 125th LEDs is lighted, the color of it is the color index 1
2. Parameters: The meaning of the two parameters represent the LED color index and the ID number of the LED.
3. Return Value: None
4. Note: This command is suitable for one operation of a LED, especially for snake display.


### 2. control LED method 2: 0x11


|Start code |length |Cmd ID |action |command |face number |color index |Data 1 |Data 2 |Data 3 |Data 4|
|-----------|-------|-------|-------|--------|------------|------------|-------|-------|-------|------|
|0XF0 0x55  |0x09   |0x00   |0x02   |0x11    |0x01        | 0x01       |0x01   |0x02   |0x02   |0x01  |

Send ``F05509000211010101020201`` to QCoo Hardware:
1. Effect: There will be some LEDs are lighted in the color of color index 1 on face 1
2. Parameters: Data 1 - 4 stands for a LED on the selected face
3. Return Value: None
4. Note: About the data 1 - 4 details, see the Software of QCoo Team


### 3. control LED method 3: 0x14

|Start code |length |Cmd ID |action |command |Face number |color index |
|-----------|-------|-------|-------|--------|------------|------------|
|0XF0 0x55  |0x04   |0x00   |0x02   |0x14    |0x01        | 0x12       |

Send data ``F055050002140112`` to QCoo hardware:
1. Effect: All the LEDs color in face 1 turned to color index 0x12
2. Parameters: Face number 0x01 represents the first face, the parameter color index 0x12 represents the color in the color pallet.
3. Return Value: None
4. The command is suitable for brushing a single face, making special effects.


### 4. refresh display: 0x03

|Start code |length |Cmd ID |action |command |
|-----------|-------|-------|-------|--------|
|0XF0 0x55  |0x03   |0x00   |0x02   |0x03    |

Send F05503000203 to QCoo hardware:
1. Effect: force refresh of the current display 
2. Parameters: The command has no parameters.
3. Return Value: None
4. Note: This command is not used frequently, because every command refreshes the display.


### 5. set the brightness: 0x20

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x04    |0x20     |

Send ``F0550400020420`` to QCoo hardware:
1. Effect: the brightness of all LEDs will be changed to 0x20
2. Parameters: The global brightness is set to 32 dec (0x20 Hex), supported values 0x0A-0xFF.
3. Return value: F0 55 01 00 10 0D 0A
4. Note: the display will be refreshed immediately. Brightness of e.g. 0x00 will not accepted, because the minimum brightness is 0x0A (decimal 10). This prevent too dark LEDs, and subsequent commands are still visible.

### 6. special effects switch: 0x05

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x05    |0x01     |

Send ``F0550400020501`` to QCoo hardware:
1. Effect: switch to the special effects mode, and show the effects of the special effects No 1
2. Parameters: parameter the effects number, 0x00 is no effect, 0x01 - 0x04 are currently implemented
3. Return value: F0 55 01 00 10 0D 0A
4. Note: You have to send F0550400020500 with parameter = 0x00 to stop special effect mode if you want display something else.

### 7. shut down: 0x15

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x05    |0x0A     |

Send ``F055040002050A`` to QCoo hardware:
1. Effect:  All the QCoo faces will be turned Red and the QCoo will shut down, no return value.
2. Parameters: parameter 0x0A no real use, the only purpose is to prevent a shut down by mistake.
3. Return value: F0 55 01 00 10 0D 0A
4. Note: If you have powered off the QCoo, you have to shake it to power on again, meantime all the actions will not respond.

### 8. motor shake: 0x16

|Start code |length |Cmd ID |action |command |shake intensity|shake time|
|-----------|-------|-------|-------|--------|---------------|----------|
|0xF0 0x55  |0x05   |0x00   |0x02   |0x16    |0x7D           |0xFF      |

Send ``F055050002167DFF`` to QCoo Hardware:
1. Effect: After receiving the command the motor starts to shake, the intensity of vibration is 0x7D (medium), the duration of vibration is 0xFF;
2. Parameters: shake intensity from 0x00 - 0xFF (reversed), 0x0=Hard/0xFF=Off, shake time from 0x00 - 0xFF
3. Return value: F0 55 01 00 10 0D 0A
4. Note: The motor shake is independent and does not affect other commands

### 9. set the connection status: 0x20

|Start code |length |Cmd ID |action |command|parameter|
|-----------|-------|-------|-------|------ |---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x20   |0x01     |

Send ``F0550400022001`` to QCoo hardware
1. Effect: QCoo will work normally
2. Parameters: The meaning of the parameter = 0x01 is that the smart device is actively connecting to the QCoo hardware. The meaning of parameter 0x00 is that the smart device is  disconnecting from QCoo
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: All commands will not work until connection is established. New firmware version could cancel connections. After a smart device sent F0550400022000 to QCoo is is disconnected and if you don’t touch it for 3 minutes, QCoo will turn itself off.


### 10. save the brightness: 0x45

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x45    |0x3c     |

Send ``F055040002453C`` to QCoo hardware:
1. Effect: There will no visible effect. This operation  save the brightness value to EEPROM. After a restart the brightness value will not lost.
2. Parameters: The parameter stores the brightness, from 0x0A to 0xFF (the actual effect starts at 0x0A)
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: This command stores the information in the EEPROM. On power-up QCoo will take the brightness value as default brightness
.

### 11. dice mode: 0x42

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x42    |0x01     |

Send ``F0550400024201`` to QCoo hardware:
1. Effect: QCoo hardware will enter the dice mode, pick up QCoo and use it as a dice.
2. Parameters: There are two parameters values:  0x01 dice mode On, 0x00 exit dice mode
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: If you want to exit dice mode you have to send F0550400024201to quite .


### 12. initialize the gyroscope: 0x43

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x43    |0x01     |

Send ``F0550400024301`` to QCoo Hardware:
1. Effect: QCoo will automatically initialize the gyroscope sensor
2. Parameters: 0x01 has no meaning, just to prevent false triggering.
3. Return Value: F0 55 01 00 10 0D 0A
4. Note: Sometimes the IIC bus will break during effect mode the gyroscope sensor communication. In this case send the command to resume the communication of gyroscope data.


### 13. query voltage: 0x17

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x17    |0x0A     |

Send ``F055040002170A`` to QCoo hardware:
1. Effect: QCoo will return the voltage 
2. Parameters: 0x0A has no meaning, just to prevent false triggering.
3. Return value: voltage value, for example: 4.2. The return value type is a String!
4. Note: This voltage value can roughly reflect the battery power status. The return type is String type.

### 14. query charging status: 0x18

|Start code |length |Cmd ID |action |command |parameter|
|-----------|-------|-------|-------|--------|---------|
|0xF0 0x55  |0x04   |0x00   |0x02   |0x18    |0x0A     |

Send ``F055040002180A`` to QCoo Hardware:
1. Effect: QCoo will return the Charging State
2. Parameters: 0x0A has no meaning, just to prevent false triggering.
3. Return value: The charge status value: "0" means no charge, "1" represents charging.
4. Note: Smart device shall Not use this command. Only developers shall query the charging state if necessary.


# QCoo Wireless upgrade (google translated from *.docx file) 

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


# QCoo Hardware parts

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
