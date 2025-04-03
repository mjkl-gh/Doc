
# Powerwall

TLDR: [zonnepanelensuper](https://zonnepanelensuper.nl/product/victron-energy-ess-set-14-4kwh-9kva-3-fasen/) sells my shopping list as a single kit. However, its probably possible to get a better price

# Preparations

As in 2027 feeding back electricity is going to be much more expensive, I've started looking into a home powerwall more seriously. As I already own a server rack, I prefer something that would fit in there. Currently my double UPS takes up 4U of rack space. If a powerwall would also function as UPS that would clear up some space. 

Learning from my collegue it's currently decently interesting to "trade" in energy using a dynamic contract.

Also we currently have an installation of 4kWp and consume 6000kwh of energy yearly, not including heat pump, but including the PHEV car. 

We can use this table to get the minimum size. It lists the maximum useful capacity for the house only.  Having more is interesting only for trading and/or car charging

![table](https://www.memodo.nl/m/fileadmin/WP_Import/nl/uploads/2023/04/Scherm_afbeelding-2023-04-05-om-11.05.45.png)

As I don't have parking space on my property I would prefer to charge my car as quick as possible whenever the spot in front is available.

Eventually it boils down to these requirements:

- Local access with open source or replaceable components
- 6kwh of storage ([link](https://www.memodo.nl/m/batterijopslag/thuisbatterijen/capaciteit-thuisbatterij/))
- 19" rack mounted
- Double function as UPS for at least the server rack
- Eventually three-phase, but maybe single-phase as long as the car and solar inverter are 1P
- capable of delivering 3,7kW of 1P charging and eventually 11kW of charging on 3P 
## Fire extinguisher
Althoughe lifepo4 batteries are in an entire different class when it comes to safety than their li-ion counterparts. We are still storing high density energy in an electrical installation. Fire is a risk best not underestimated. [Zonnepanelensuper](https://zonnepanelensuper.nl/product/doe-het-zelf-48v-accubehuizing-v2/) Seems to have therefore integrated an aerosol fire extinguisher into the battery case.

However, it does not seem clear from the outset what type or brand.

Prefarably I'd take the following precautions:
- Check if all the fire extinguishers in my home are rated for B type fires
- Automatic fire extinguisher per battery case
- Automatic fire extinguisher above the entire installation (server rack + inverters)
- Plan a yearly check of fire fighting equipment
# Case

The goto for a BMS seems to be a JK BMS 

The company EEL sells batteryboxes that neatly fit into a server rack. But also vertical wall mounted cases, if that's more your need.

They also integrate a DIN sized breaker in the box and include the JK BMS ,and screen if required. Obviously I'd replace the breaker with something European.

For now the [v4](https://www.eelbattery.com/eel-48v-16s-v3-server-rack-diy-unit-box-built-in-jk-bluetooth-2a-active-balance-bms-stackable-type-p5372920.html) seems like the best option as the v5 misses the breaker

# BMS

For now the JK BMS seems like the better option as it neatly integrates into most inverters

Another party to consider is DALY, however it doesn't seem to integrate that well.

I'm also still hoping for an Open source version.

## Batteries

My colleague got his batteries at Docan energy.

However, lately https://www.nkon.nl/ seems to also deliver cells at a decent price.

It will have to be determined when buying what cells offer the best price. As I think I can have 2 packs I might not need maximum capacity cells

Some considerations
- A quality might actually be something less, but relabeled
- A quality is not neccesary because this is not a car, but a home installation

Options;
- LF280K V3
- [Envision HC-L315A](https://www.nkon.nl/envision-hc-l315a-3-2v-315ah-1008wh.html)
- [Eve MB31 Prismatic](https://www.nkon.nl/eve-mb31-prismatic-314ah-lifepo4-3-2v-single-stud-grade-a.html)

## Inverter

To have a proper "ESS" installation we need a decent grid tie inverter

As I would like to get this at a well known, trusted supplier I've the chosen the modular Victron system

Consisting of

- Victron Energy 3-Phase Energy Meter
- Victron MultiPlus II 3000VA/48V
- Victron Energy Cerbo GX MK2

It's debatable If I should get the Multiplus II GX or non-GX version. For 1P the GX is definitely cheaper. But also, for 3P getting a seperate Cerbo CX seams a bit more expensive.

Drawbacks of one or the other need to be investigated
## (bonus) Solar panels
As I now already have a low voltage inverter installation it's probably interesting to wire future solar panels directly into this installation

It should be quite cheap to get second hand solar panels and wire them into the circuit using a [victron mppt tracker](https://www.nkon.nl/victron-energy-scc110020160r-smartsolar-mppt-100-20.html)
