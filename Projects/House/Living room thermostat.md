#SlokkerSpaarhuis
Our house has/used to have a pretty elaborate heating system more like the american HVAC than what dutch house's are used too. The complete system is the so called "Slokker spaarhuis."
When we moved in, only the gas heater was by a heater or so called "CV-ketel" but everything else was still the original, now antiquated, system.  Even the replaced heater, an Itho Kli-max 2, was already 22 years old and begging for a replacement

Already in the first winter the system seemingly died on us. Or at least the ventilation part did. And in HVAC if the ventilation dies, the heating also dies. The reason for this will/~~should~~ be documented elsewhere on this repo and hopefully linked here #ToDo

#esphome
Regardless the pcb inside the ventilation was replaced with a KMP electronics [prodino-esp32ex-ethernet](https://kmpelectronics.eu/products/prodino-esp32-ethernet-v1/) running [ESPhome](https://esphome.io/) and a programming inspired by my experience with PLC's. This also will/~~ìs~~ documented elsewhere #ToDo 

The input for this heating controller is still the old "Slokker Spaarhuis" thermostat using physical contacts to signal heating and ventilation requirements. This project aims to replace the old thermostat.

![[Projects/House/images/Pasted image 20250219234601.png]]]]

#smartknob
The idea is to use the [Seedlabs smartknob devkit 0.1](https://store.seedlabs.it/products/smartknob-devkit-v0-1) to create a thermostat with feedback to control the home. This is a display and rotary encoder with esp32 and haptic feedback by brushless motor inspired by [Scotbezz1](https://github.com/scottbez1/smartknob) seen in [this](https://www.youtube.com/watch?v=ip641WmY4pA) video

The current project idea is
- Smartknob interface in the living room
- Communicate with heating controller through the original cable
- Make the heating controller talk opentherm to the Kli-max 2
- As always use wired communication

The heating controller lacks real control of the heater. Fortunately, the ancient thing supports opentherm to control the water temperature and some other settings. Using - [Ihor Melnyk’s OpenTherm Adapter](http://ihormelnyk.com/opentherm_adapter) the heating controller should get more finegrained control of the heating. These will be connected to the exposed pins inside the prodino-esp32ex-ethernet.

For the wired communication the following alternatives were considered:
- Modbus RTU (rs485)
- Opentherm
- UDP over Ethernet

Currently UDP over Ethernet is selected. This because direct communication over UDP seems the simples solution supported by esphome. Technically it would introduce a dependency on my router for offering DHCP. However technically using multicast wouldn't require this. Future research will need to show this. To sum up the advantages would be:

- Direct communication and updating via wired ethernet for the thermostat
- Easiest integration for sharing esphome sensors inter-device
- No other physical runtime dependencies like DHCP-server or Home assistant running
- Possible KNX over IP support

The original cable is a 6 strand cable leaving room for running 4 strand 100mbit or 10mbit ethernet. It's probably untwisted. So I hope this will work because it's a short run (12m max). the 2 other strands leave room for powering the device. 

The original smart knob will need an ethernet PHY connected to communicate over ethernet. Since the devkit has 3 i2c connections a 