# HL2-PA70

## 70W Power Amplifier for the Hermes-Lite 2.x

This is a work in progress to create a 70W power amplifier to be used together with the Hermes-Lite 2.x using the commercial DIY Kit 70W SSB linear HF Power Amplifier, a Low Pass Filter, and an Arduino board, to control it all.

The amplifier is based on an "70W SSB Linear HF Power Amplifier" you can find on eBay, Banggood, AliExpress, etc. This is the schematic:

![70W SSB Linear HF Power Amplifier](https://github.com/ea3igt/HL2-PA70/blob/main/Images/70W%20SSB%20Amplifier%20circuit.png?raw=true)


I did some modifications on the original board to adapt it to the new transistor and to my requirements:

1. I changed the two included IRF640 Single Mosfets, for one MRF-9120 (Double RF Power Mosfet) that fits perfectly in the PCB and is more reliable because the heat transference is more convenient due to the form factor (the PCB accept both formats, the TO-220 package for the IRF640 ant the NI-860 package for the [MRF-9120](https://www.nxp.com/docs/en/data-sheet/MRF9120.pdf))
2. I changed the R7 to a lower value (100 Ohm) to get the proper Bias level (~4.2 volt)
3. I added the 15 Ohm R6 (see the schematic) for better input SWR adaptation (modification suggested by OE1CGS)

This 70W SSB linear HF Power Amplifier needs a Low Pass Filter (LPF) to be compliant, removing the harmonics (there is an Input-Output connector in the PCB to use an external LPF) and I choose a very simple "HF low pass LPF 3.5M-30Mhz for Ham Radio CW FM" that fits perfectly with the power amplifier and does its job. This LPF has 4 bandpass filters for 80m, 40m, 20-17m and 15-10m, selectable connecting the specific pin to ground.

I wanted to use this Power Amplifier for my Hermes Lite 2 (HL2) and I wanted a fully automatic operation, so I decided to use an Arduino Nano to control it all:

1. Reading the I2C bus from the HL2 to decode the band
2. Selecting the appropiate filter on the LPF board
3. Using the EXTTR signal to activate (bias) the amplifier (PPT)
4. Sensing the temperature of the transistor using an NTC
5. Drive the speed of the fan using Pulse-width modulation (PWM) depending on the transistor temperature
6. And display Power Amplifier basic information using an OLED mini-screen

This is the complete block diagram of the amplifier I designed:

![PA70 Block Diagram](https://github.com/ea3igt/HL2-PA70/blob/main/Images/PA70%20Block%20Diagram%20v2.1.2.JPG?raw=true)

## V1.0: First Tests and Control Board Prototype

Based on the information published by Alex Pressl in [Easyeda](https://easyeda.com/pressl.alex/experiment_HL2_Arduino_I2C), and inspired by his work, I began to work on my first prototype of the Control Board to test the I2C connection from my HL2 to the initial "Control Board", listening to the I2C messages that HL2 uses to communicate from the main board to the internal LPF filter (N2ADR). 

![PA70 Control Board](https://github.com/ea3igt/HL2-PA70/blob/main/Images/First%20Prototype%20v1.0.2.jpg?raw=true)

During this first prototype, I also tested the Temperature Sensor (a common thermistor NTC) and the Pulse Width Modulation (PWM) generated by the Arduino to control the fan speed accordingly.

## V2.0: PA70 First Usable Version

This is the schematic of the first version of the Control Board:

![PA70 Control Board Schematic](https://github.com/ea3igt/HL2-PA70/blob/main/Images/Control%20Board%20v2.1.2.JPG?raw=true)

And this is the first fully functional version of the Power Amplifier with the LPF and the Control Board:

![PA70 Inside View V2.1](https://github.com/ea3igt/HL2-PA70/blob/main/Images/HL2-PA70%20v2.1.0.JPG?raw=true)

## V3.0: PA70 First Finished Version

Based on the experience accumulated during first prototype tests and the first usable Control Board, I designed my self PCB. This is the final schematic I implemented for the Control Board built around the Arduino Nano:

![PA70 Control Board Schematic v3.0](https://github.com/ea3igt/HL2-PA70/blob/main/Images/Control%20Board%20Schematic%20v3.0.1.JPG?raw=true)

This is the final Control Board with all the components and the Arduino Nano:

![PA70 Control Board Schematic v3.0](https://github.com/ea3igt/HL2-PA70/blob/main/Images/Control%20Board%20v3.0.1.jpg?raw=true)

And this is the final Power Amplifier complemented with the LPF and the Control Board:

![PA70 iNSIDE vIEW v3.0](https://github.com/ea3igt/HL2-PA70/blob/main/Images/HL2-PA70%20v3.0.0.JPG?raw=true)

![PA70 Frontal View V2.1](https://github.com/ea3igt/HL2-PA70/blob/main/Images/HL2-PA70%20Enclosure%20v2.1.0.jpg?raw=true)

You can find in this repository:

- The Arduino Nano code for the Control Board ([Software](https://github.com/ea3igt/HL2-PA70/tree/main/PCB%20Board))
- The Control Board KiCad files for PCB design and the Schematic ([PCB Board](https://github.com/ea3igt/HL2-PA70/tree/main/PCB%20Board))
- The Bill of Materials to implement the Control Board ([Control Board BOM](https://github.com/ea3igt/HL2-PA70/blob/main/PCB%20Board/Control%20Board%20v2.1%20BOM.xlsx))

### V3.0 Release Notes

Please, take into account the following notes if you want to implement this project:

-	The Control Board program running on the Arduino is very basic and, for sure, it can be improved and optimized, especially how it handles the interrupts from the I2C bus or from the EXTTR signal coming from the HL2.
-	The Control Board can be powered through the USB port or (recommended) connecting it to an external power supply. If this last option is used, supply 6+ Volts (6..7 volts recommended).
-	The Control Board is designed and implemented to use an SSH1106 chip based, 128x64 OLED Display (like this one: [OLED Display](https://www.amazon.com/HiLetgo-pulgadas-SSH1106-SSD1306-Display/dp/B01MRR4LVE/)), but, of course, it can be adapted to other displays.
-	The Low Pass Filter (LPF) control mechanism is based on driving the specific band pin to ground to select the adequate filter. For my implementation I only need 4 pins to do the filter selection, but the Control Board is designed to control up to 8 separate filters. 
-	Related to the Band selection, the HL2 selects up to 6 bands: 160m – 80m – 60/40m – 30/20m – 17/15m – 12/10m. You must map all these bands to your specific band selection in the decodeBand() function on the Arduino Sketch.
-	I decided that all PTT control will be done by the Control Board because I wanted to add some protection capabilities in a future version (Max current, SWR limit, Temperature, etc.), so the EXTTR signal (PTT) coming from the HL2 does not go directly to the PTT pin on the Power Amplifier board, but it goes to my Control Board. If you want, for simplicity, you can connect the EXTTR or PTT signal coming from the HL2 directly to the Power Amplifier.
-	Different control programs for HL2 have different behavior for Band Selection. I have only tested the Control Board software with SparkSDR, SDRConsole and PowerSDR.
-	I store the last selected Band on the Arduino’s EPROM to “remember” and select the last used band when the Control Board powers up.
-	The Temperature sensor is simple, but it works perfectly. I use a regular NTC Thermistor, and I tested a couple of them with the same good results, so I understand that you are not going to have problems with it, perhaps only some adjustments.
-	The temperature control implemented in the Control Board drives a Pulse Width Modulation fan to modify its speed depending on the temperature, so you will need a fan with this capability if you want to use this functionality.
-	I have a “#define TEST” software switch in the Arduino Sketch to be used if you want to debug the program. You can get all the debugging information in the Serial Monitor. Remove or comment this sentence for production.
