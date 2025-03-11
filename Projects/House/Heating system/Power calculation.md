
## Bathroom

![[Pasted image 20250309224447.png]]

According to the calculater at [hudsonreed](https://nl.hudsonreed.com/hudson-reed-radiator-vermogen-calculator)

509w for 4.8m^2 including windows, 2 exterior, brick, insulated walls and an attic with 100mm+ insulation above it.

Other calculations (like [badkamerxxl](https://www.badkamerxxl.nl/verwarming/radiator-vermogen-berekenen)) assume 85w per m^3 for 24 degrees, which is advised for a bathroom. Which would result in 849w

For now we assume for calculation:

		800w

## Bedroom 2
![[Pasted image 20250309230435.png]]

Since this is a bedroom we would like to also cool this room. Therefore we require a cooling capacity and heating capacity

We assume a factor of 40 according to [warmtepompgigant](https://www.warmtepompgigant.nl/verwarmen-en-koelen/capaciteit-airco-berekenen/)

For 9.8m^2 and a height of 2.5m would come down to a cooling capacity of

		980w

And for heating according to [cvtotaal](https://www.cvtotaal.nl/radiatoren/berekenen) we need 1450w at "Good" insulation and 18 degrees

Other websites quote 70w at 18 degrees and would come down to 1680w

So for now we assum:

		1600w
## Master bedroom
![[Pasted image 20250309233532.png]]
The master bedroom has an adjacent walk in closet we also need to take into account.

Since this is another bedroom we re-use the numbers from bedroom no 2:

Cooling capacity

22.9m^2 x 2.5m x 40 = 

		2290w


Heating capacity

22.9m^2 x 2.5m x (1600/9.8*2.5) = 

		3840w

According to this [table](https://gidsduurzamegebouwen.brussels/leidingen-dimensioneren-om-drukverliezen-beperken) at a delta T of 20 degrees for ventilated convectors, like fan coils, a DN15 sized tube can carry 7kw of heat at vmax of 0.4m/s

Therefore we need to use [Henco Alupex meerlagenbuis ISO9 10mm rood](https://www.warmteservice.nl/Installatiemateriaal/Leidingsystemen/Meerlagenbuis/Henco-Alupex-meerlagenbuis-ISO-10-mm-rood/p/48670225)


## Insulation calculation

For calculating the insulation thickness I've tried to write the following script:

```python
import math

def calculate_surface_temperature(d_inner, t_inner, t_ambient, h, emissivity, thermal_conductivity_inner, thickness_inner, thermal_conductivity_insulation, thickness_insulation):
    # Constants
    sigma = 5.670374419e-8  # Stefan-Boltzmann constant (W/m^2*K^4)
    
    # Convert temperatures to Kelvin
    t_inner_k = t_inner + 273.15
    t_ambient_k = t_ambient + 273.15
    
    # Convert diameters to meters
    d_inner_m = d_inner / 1000  # Convert mm to m
    thickness_inner_m = thickness_inner / 1000  # Convert mm to m
    thickness_insulation_m = thickness_insulation / 1000  # Convert mm to m
    
    d_mid_m = d_inner_m + 2 * thickness_inner_m  # Outer diameter of inner PE-X tube
    d_outer_m = d_mid_m + 2 * thickness_insulation_m  # Outer diameter of insulation
    
    # Log mean radii
    r_inner = d_inner_m / 2
    r_mid = d_mid_m / 2
    r_outer = d_outer_m / 2
    
    # Thermal resistances
    r_conduction_inner = math.log(r_mid / r_inner) / (2 * math.pi * thermal_conductivity_inner)
    r_conduction_insulation = math.log(r_outer / r_mid) / (2 * math.pi * thermal_conductivity_insulation)
    r_convection = 1 / (h * 2 * math.pi * r_outer)
    
    # Radiative heat transfer resistance
    r_radiation = 1 / (emissivity * sigma * (t_ambient_k**4) * 2 * math.pi * r_outer)
    
    # Total thermal resistance
    r_total = r_conduction_inner + r_conduction_insulation + (r_convection * r_radiation) / (r_convection + r_radiation)
    
    # Heat flux per unit length (assuming steady-state conduction)
    q_per_length = (t_inner_k - t_ambient_k) / r_total
    
    # Surface temperature (considering convection and radiation)
    t_surface_k = t_inner_k - q_per_length * (r_conduction_inner + r_conduction_insulation)
    
    # Convert back to Celsius
    t_surface_c = t_surface_k - 273.15
    
    return t_surface_c

# Given values
d_inner = 20  # mm
thickness_inner = 0.1  # mm (PE-X tube)
thickness_insulation = 0.1  # mm (Insulation)
thermal_conductivity_inner = 0.4  # W/(m*K) for PE-X
thermal_conductivity_insulation = 0.43  # W/(m*K) for insulation
t_inner = 7  # degrees Celsius
t_ambient = 27  # degrees Celsius
h = 10  # W/(m^2*K)
emissivity = 0.93  # Emissivity of the pipe surface

# Compute surface temperature
t_surface = calculate_surface_temperature(d_inner, t_inner, t_ambient, h, emissivity, thermal_conductivity_inner, thickness_inner, thermal_conductivity_insulation, thickness_insulation)
print(f"The surface temperature of the pipe is {t_surface:.2f}°C")


```