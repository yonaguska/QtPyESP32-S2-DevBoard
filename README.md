# QtPyESP32-S2-DevBoard

Development board for QT Py ESP32-S2 projects using Qwiik connected modules (Adafruit style). This document describes the Adafruit QT Py ESP32-S2 development board, Version 1. Originally developed to be a leak detection system. Repurposed as a general purpose development system useful for developing other IoT applications.


# HARDWARE


## THE MAIN BOARD CONTAINS THE FOLLOWING HARDWARE
- Adafruit ESP32-S2 (and S3) processor (socketed: compute)
- Adafruit microSD Card BFF modules (socketed: storage)
- Adafruit LiIon Charger BFF module (socketed: power)
- DS3231 w/EEPROM (soldered: realtime clock & storage)
- TPL5110 Low Power Timer Breakout (socketed: power control)
- Lipo Charger (soldered: solar or USB power in)
- LoRa (socket only)
- Any Qwiik connected device, they can be daisy chained or tied
  in with Adafruit Qwiik/STEMMA QT 5 Port hub or Adafruit 
  STEMMA QT/Qwiik I2C multiplexer.

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

The code accommodates adding hardware, detecting it, and using it automatically
It also allows the hardware to be on existing I2C pins or a STEMMA QT connector.

A typical hardware module initializes and readies the sensor, handles reading
values and making them ready for the main code.

The main code can upload values via WiFi or LoRa; modules exist for each.

**This code was built for CircuitPython 8 and upgraded for version 9.

## QtPy ESP32-S2
![QT Py ESP32-S2](https://www.adafruit.com/product/5325)

>>> dir(board)
['__class__', '__name__', 'A0', 'A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7',
'BOOT0', 'BUTTON', 'D0', 'D16', 'D17', 'D18', 'D35', 'D36', 'D37', 'D40',
'D41', 'D5', 'D6', 'D7', 'D8', 'D9', 'I2C', 'MISO', 'MOSI', 'NEOPIXEL',
'NEOPIXEL_POWER', 'RX', 'SCK', 'SCL', 'SCL1', 'SDA', 'SDA1', 'SPI',
'STEMMA_I2C', 'TX', 'UART', 'board_id']
>>>

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

There are two onboard LEDS used to monitor the operation of the TPL5110. 
  - The GREEN LED indicates there is power to the QT Py.
  - The RED LED shows the DONE signal used to tell the TPL5110 to
    kill power to the QT Py.

## BOARD HEADERS

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
              
QT STEMMA Cable wiring:
- YELLOW --- BLACK  GROUND
- BLUE ----- RED  V+  (3-5V)
- RED ------ BLUE  SDA (data)
- BLACK ---- YELLOW  SCL (clock)
  

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
  
  I will leave it to you to read about these subsystems, of you want
  to learn the nitty gritty of how to use them to build a nifty
  monitoring system. 
   
  
ORIGINAL FILES FOUND AT

CircuitPython Scripts

  /Users/kfranks/Documents/Development/Embedded/Processors/Adafruit/leak_detector
  
Github for yonaguska (me)

  https://github.com/yonaguska/QtPy_ESP32-S2

This be a new line
