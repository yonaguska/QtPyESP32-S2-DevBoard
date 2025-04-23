# QtPyESP32-S2-DevBoard

Development board for QT Py ESP32-S2 projects using Qwiik connected modules (Adafruit style). This document describes the Adafruit QT Py ESP32-S2 development board, Version 1. Originally developed to be a leak detection system. Repurposed as a general purpose development system useful for developing other IoT applications.


# HARDWARE
![circuit_front](https://github.com/user-attachments/assets/48aa1013-5699-4383-acf4-c0b02afc871c)

## THE MAIN BOARD CONTAINS THE FOLLOWING HARDWARE
- Adafruit ESP32-S2 (and S3) processor (socketed: compute) 
- Adafruit microSD Card BFF modules (socketed: storage)
- Adafruit LiIon Charger BFF module (socketed: power)
- DS3231 w/EEPROM (soldered: realtime clock & storage)
- TPL5110 Low Power Timer Breakout (socketed: power control)
- Lipo Charger (soldered: solar or USB power in)
- LoRa (socket only)
- Any Qwiik connected device, they can be daisy chained or tied in with Adafruit Qwiik/STEMMA QT 5 Port hub or Adafruit STEMMA QT/Qwiik I2C multiplexer.
- And there's a 4000mAh Lipo Battery tucked under the development board, strapped onto the base plate

### LiPo 4000mAh
![big_lipo](https://github.com/user-attachments/assets/93fde174-d9cb-4ef2-896b-2c65c9b8b19a)

## CircuitPython MODULES FOR HARDWARE INCLUDE
- i2c       (stemma qt and onboard)
- neopixel  (onboard indicator)
- aht20     (temp and humidity)
- sht40     (temp and humidity)
- bme280    (temp, humidity, pressure, and altitude)
- bme680    (temp, humidity, pressure, altitude, and voc/gas)
- ds3231    (timestamp, set via NTP)
- wifi      (if present)
- lc709203f (feather2 s2/s3)
- ina260    (option for testing)
- 24lc32    (4kB EEPROM on the DS3231)
- soil_probe (separate probe; power and analog input)
- battery    (external battery voltage monitor...useful for solar chargers)

| QT Py ESP21-S | SD Card       |   LiPo BFF    | DS231/EEPROM  |  TPL5110      |  Lipo Charger |
|---------------|---------------|---------------|---------------|---------------|---------------|
| ![onboard_qt_py_esp32-s2](https://github.com/user-attachments/assets/f864b3e2-97d7-4365-92f3-48f809b64ab8)| ![onboard_sd_card](https://github.com/user-attachments/assets/4164b69a-bad4-46fe-b729-8cbbb459d683)| ![onboard_lipo_bff](https://github.com/user-attachments/assets/df41f840-c197-46d6-b28f-42262cf5f1ba)| ![onboard_ds3231](https://github.com/user-attachments/assets/2fbe5cc8-6fcf-49b9-98c0-46ab0ccb2d06)| ![onboard_tpl5110](https://github.com/user-attachments/assets/03001504-d84c-4a5c-85bc-ae9f8518e945)| ![onboard_lipo_charger](https://github.com/user-attachments/assets/ee6690be-31ec-40cd-a2bd-618d3efd3ad6)|

| Soil Probe    | BME680        | BFF LiPo Conn| Batt Monitor   | QtPy to Brd   | Test Points   |
|---------------|---------------|---------------|---------------|---------------|---------------|
| ![soil_moisture_detector](https://github.com/user-attachments/assets/f083d882-83ca-429c-97a5-d0cf2d38b0e1)| ![bme680](https://github.com/user-attachments/assets/3b5c076a-b7cd-4c1b-8d07-61b380ec3ba7)| ![bff_lipo_connector](https://github.com/user-attachments/assets/7fb17d11-f876-4950-9a34-def3e16dc451)| ![monitored_battery_connector](https://github.com/user-attachments/assets/51e9c516-6eec-4c05-93b9-3079225d4b45)| ![qtpy_to_board_qwiik_connection](https://github.com/user-attachments/assets/c375552e-94bc-470d-b216-b53053bd7008)| ![test_points](https://github.com/user-attachments/assets/7c40e494-5bac-458b-b12e-10a01efabb1d)|






The code accommodates adding hardware, detecting it, and using it automatically
It also allows the hardware to be on existing I2C pins or a STEMMA QT connector.

A typical hardware module initializes and readies the sensor, handles reading
values and making them ready for the main code.

The main code can upload values via WiFi or LoRa; modules exist for each.

**This code was built for CircuitPython 8 and upgraded for version 9.

## QtPy ESP32-S2
![Pinout](https://github.com/user-attachments/assets/caeb8cc6-a78f-4118-908b-a5d25b4c8360)

>>> dir(board)
['__class__', '__name__', 'A0', 'A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7',
'BOOT0', 'BUTTON', 'D0', 'D16', 'D17', 'D18', 'D35', 'D36', 'D37', 'D40',
'D41', 'D5', 'D6', 'D7', 'D8', 'D9', 'I2C', 'MISO', 'MOSI', 'NEOPIXEL',
'NEOPIXEL_POWER', 'RX', 'SCK', 'SCL', 'SCL1', 'SDA', 'SDA1', 'SPI',
'STEMMA_I2C', 'TX', 'UART', 'board_id']
>>>
>>> 
## QT Py ESP32-S2 DOCUMENTATION
[Adafruit QT Py ESP32-S2 Product Page](https://www.adafruit.com/product/5325)


# BASIC OPERATION OF THE POWER SYSTEM

A USB cable or Solar panel is applied to the Lipo charger circuit, which in turn charges a large LiPo battery set underneath the development board. The LiPo charger input range is 4.5-5.5V.
  
The output of the battery, run back through the LiPo charger, powers the TPL5110 and, when enabled by the TPL5110, sent to the LiIon Charger BFF through a cable soldered on the board and plugged into the LiIon Charger BFF. 
  
The LiIon Charger BFF serves to power the QT Py ESP32, or when a USB cable is plugged into the QT Py, charges the LiPo battery. This module insures that the QT Py is always powered up (I think)
   
## POWER FLOW
 
  |-> Power taken from USB or 4.5-5.5V solar panel into LiPo Charger


    -> LiPo Charger charges LiPo battery and outputs circuit power


        -> TPL5110 controls battery power to circuit


            -> LiIon Charger takes controlled power and supplies ESP32


               ->  QT Py ESP32 powered by LiPo and outputs 3V
  
## SENSOR AND PERIPHERAL CONNECTIONS
  
The SD card connects via SPI, QT Py's MO/MI/SCK pins drive it and QT Py's TX pin selects it.
  
There is a header to support an untested LoRa module, connected by SPI. It also uses the QT Py's MO/MI/SCK pins, gets selected  by QT Py's SCL pin and reset by the SDA pin. (NOTE: we use the STEMMA QT I2C connector for our sensors and other peripherals,  so we sacrificed these pins to LoRa). The QT Py's A0 pin can be used to supply a signal to either the LoRa module's EN or G0 pin, as selected by a header jumper on the board. This is a work in progress.
  
There is a header to support the connection of a soil moisture sensor, It powers the sensor with 3V and connects the output of the sensor to the A3 pin of the QT Py, where it's read by an analog pin.
  
There's a connector for the attaching an external battery. It's labeled 12V but it could really monitor any voltage source under 12V. It goes to a 12k/3k resistive divider wherein we will see 1/5th of the battery's voltage on the A1 pin of the QT Py. It would set that pin as an analog input for measurement. Similarly the A2 pin connects to the LiIon Charger BFF's battery monitoring pin, for measurement of our main LiPo battery.
  
Sensor and other peripherals are connected to the QT Py's I2C STEMMA QT Qwiik connector. (NOTE: to use the DS3231, you must run a STEMMA QT cable from the QT Py to one of the two Qwiik connectors on the board, because it's not connected to the SDA/SCL pins of the QT Py). Search Adafruit for stemma cables to learn more, JST SH 4 pin connectors.
    
## BOARD LEDS
![LEDs](https://github.com/user-attachments/assets/b747277a-76f4-4818-a4ea-40c473ca6564)

There are two onboard LEDS used to monitor the operation of the TPL5110. 
  - The GREEN LED indicates there is power to the QT Py.
  - The RED LED shows the DONE signal used to tell the TPL5110 to
    kill power to the QT Py.

## BOARD HEADERS
![Motherboard_routed_v1](https://github.com/user-attachments/assets/e84f69ad-aaf6-4419-a646-7981aaeb460d)
- Test Points SYSTEM GND
              QT Py 3V
              QT PY 5V
              12 V BATT IN (for monitoring a 12v battery)
              12V BATT MON
              OUT+  (LiPo charger OUT+)
              LIPO+ (LiPo charger battery+)
              BFF+  (TPL5110 DRV out to power circuitry)
              
- LoRa EN or G0 select, if used, jumper from the common pin to your selection.
  
- RESET, add header pins or solder wires to an external switch that allows the TPL5110 to be triggered, just like using the onboard switch. This is to facilitate buttoning up the board in a box.

## SCHEMATIC
![devboard_schematic](https://github.com/user-attachments/assets/c0fee5d6-2de6-4f86-830f-009a4e0d97c0)

## ROUTING
![devboard_board](https://github.com/user-attachments/assets/6de4a6ff-e7ad-4b3b-9646-4ba0055cca2b)
              
## QT STEMMA Cable

  | Qwikk STEMMA Cable |
  |--------------------|
  | ![Qwiik_STEMMA_cable](https://github.com/user-attachments/assets/32dc17ca-90d4-477e-ba67-1c1820f1c53c)|

- BLACK  GROUND
- RED  V+  (3-5V)
- BLUE  SDA (data)
- YELLOW  SCL (clock)
  
# SOFTWARE

## CODE OVERVIEW

  The code.py file is a little over 800 lines of CircuitPython code and comprises the main logic of the development code. It is meant to give a comprehensive look at how I've chosen to implement code to exercise the hardware of the development system. You can replace it with your own code as you wish. But everything works here, so you might want to save a copy of the code that comes with this development system before you overwrite it.
  
  In addition to code.py, there is a secrets.py that contains credentials used to log into the wifi system and a config.py file that can be used to manage sensors you attach.
  
  There is a lib directory that contains the various Adafruit libraries required by code.py and the various sensors I've written code for.
  
  There is a sd directory, required to support the microSD module.
  
  There are a dozen of so mod_xxx.py files that are refactored code that used to live in the code.py file. It simplifies the work needed to add another sensor to the system. The names are fairly self explanatory.
  
  This was originally written when CircuitPython was still version 8, I've had to update everything to brining it up to version 9. It all works now.
  
  From a high level view, code.py initializes the hardware of the system and then begins looking for sensors, looking on the Qwiik I2C connector and on the board's I2C pins, using known device IDs. If it find one, it enables it for later use. It initializes the sensor, reads data from it, and stores that data in a dictionary.
  
  Once it's found and read all of the sensor data, it processes it into a form it can send to the MQTT broker. It sends the data and then puts itself to sleep for approximately five minutes, going into a deep sleep more to conserve battery power. Yeah, this code started life as a remote solar-powered, battery backed-up environmental monitor. And LoRa is there to let me put the system way out in the back forty where wifi connections won't cut it. Along the way, I thought this would also be a useful development platform for IoT projects in general.
  
  And the SD card? It's for logging and whatever diagnostics I need to write when I need to troubleshoot. Especially useful if the system is not directly connected to my computer.
  
  
MODULES AND METHODS
  
  Code supporting the attached sensors, and other potentially reusable
  functions, were refactored into python modules. Most notably code
  supporting the Adafruit sensors. The following tables list the modules
  and their methods. All modules of this type are given the mod_ prefix.
  
   
                 ADAFRUIT ENVIRONMENTAL SENSORS (mod_x)
 		
  | METHOD | ahtx0 | sht40 | bme280 | bme680 | ina260 | ds3231 |
  |--------|-------|-------|--------|--------|--------|--------|
  | init	 |   X   |   X   |    X   |    X   |    X   |    X   |	
  | set		 |       |       |        |        |        |    X   |
  | read	 |	 X	 |	 X	 |    X   |    X   |    X   |    X	 |
   
               CUSTOM ENVIRONMENTAL SENSORS (mod_x)
 	
  | METHOD     | soil_probe | battery_voltage |
  |------------|------------|-----------------|
  | init       |    X       |    X            |
  | set	       |            |                 |
  | read       |    X       |    X            |
  |get_voltage |   X        |    X            |
  				
                        OTHER MODULES (mod_x)

  | METHOD           | i2c      | 24lc32   | neopixel  | diag     |
  |------------------|----------|----------|-----------|----------|
  | init             |   X      |   X      |           |          |
  | set              |          |   X      |           |          |
  | read             |          |   X      |           |          |
  | power_pixel      |          |          |   X       |          |
  | connected_health |          |          |   X       |          |
  | no_sensors       |          |          |   X       |          |
  | reset_reason     |          |          |           |  X       |
  

# FUTURE WORK

  But wait, there's more. There is functionality in code.py that 
  connects the QT Py to a MQTT broker living on a Raspberry Pi 
  RP400 host computer. This was all working a couple of years ago, 
  before I let the system sit and collect dust. In the interim, 
  I switched out my Internet provider to StarLink and the new 
  subnet number will require some work on the host to get it 
  operational again.
  
  Oddly, the code still connects to the MQTT broker and sends up
  it's data. That implies the broker is still running on the host 
  and is receiving the data. The host software consists of the TIG
  stack (telegraf/influxdb/grafana) fronted by mosquitto. The stack
  for the data flow is:
  
     mosquitto - a MQTT broker to receive development board data
     telegraph - an intermediary subsystem to retrieve incoming
                 MQTT data and stuff it into influxdb
     influxdb  - a time-series database designed to deal with
                 time ordered data, such as monitoring systems
                 generate
     grafana   - a UI designed to display numerical data in the
                 form of charts and graphs
  
  To simplify the installation and configuration of these various
  subsystem components, each is run within a docker container,
  using containers found on Docker Hub. The only customizations is
  that associated with my Raspberry Pi host system. Each subsystem
  has a web page interface found on the port conventionally used by
  it.
  
  I will leave it to you to read about these subsystems, if you want
  to learn the nitty gritty of how to use them to build a nifty
  monitoring system. 
   
  
ORIGINAL FILES FOUND AT

CircuitPython Scripts

  /Users/kfranks/Documents/Development/Embedded/Processors/Adafruit/leak_detector
  
Github for yonaguska (me)

  https://github.com/yonaguska/QtPy_ESP32-S2
 
Adding this line 
