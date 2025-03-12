## Bathroom heating/cooling requirements

![[Pasted image 20250309224447.png]]

According to the calculater at [hudsonreed](https://nl.hudsonreed.com/hudson-reed-radiator-vermogen-calculator)

509w for 4.8m^2 including windows, 2 exterior, brick, insulated walls and an attic with 100mm+ insulation above it.

Other calculations (like [badkamerxxl](https://www.badkamerxxl.nl/verwarming/radiator-vermogen-berekenen)) assume 85w per m^3 for 24 degrees, which is advised for a bathroom. Which would result in 849w

For now we assume for calculation:

		800w

## Bedroom 2 heating/cooling requirements
![[Pasted image 20250309230435.png]]

Since this is a bedroom we would like to also cool this room. Therefore we require a cooling capacity and heating capacity

We assume a factor of 40 according to [warmtepompgigant](https://www.warmtepompgigant.nl/verwarmen-en-koelen/capaciteit-airco-berekenen/)

For 9.8m^2 and a height of 2.5m would come down to a cooling capacity of

		980w

And for heating according to [cvtotaal](https://www.cvtotaal.nl/radiatoren/berekenen) we need 1450w at "Good" insulation and 18 degrees

Other websites quote 70w at 18 degrees and would come down to 1680w

So for now we assum:

		1600w
## Master bedroom heating/cooling requirements
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
import numpy as np
from scipy.optimize import newton
 
# Inputs (with placeholder for emissivity)
pipe_length = 10 # m
d_inner = 20e-3 # m (inner diameter)
t_pipe = 3e-3 # m (pipe thickness)
t_ins = 6e-3 # m (insulation thickness)
k_pipe = 0.43 # W/(m·K)
k_ins = 0.036 # W/(m·K)
h_in = 1000 # W/(m²·K) Assumed inner convection. Should be negligible due to constant flow and water
h_out = 10 # W/(m²·K) Assumed outer convection
T_coolant = 7 # °C
T_ambient = 27 # °C
epsilon = 0.93 # Assumed emmissitivy
sigma = 5.67e-8 # W/(m²·K⁴)

# Geometry
r_inner = d_inner / 2
r_pipe = r_inner + t_pipe
r_ins = r_pipe + t_ins
A_inner = 2 * np.pi * r_inner # Per unit length
A_outer = 2 * np.pi * r_ins # Per unit length

# Thermal resistances (per unit length)
R_conv_in = 1 / (h_in * A_inner)
R_cond_pipe = np.log(r_pipe / r_inner) / (2 * np.pi * k_pipe)
R_cond_ins = np.log(r_ins / r_pipe) / (2 * np.pi * k_ins)
R_total_cond_conv = R_cond_pipe + R_cond_ins + R_conv_in
  
# Heat balance equation for outer surface
def heat_balance(T_outer):
	Q_cond_conv = (T_coolant - T_outer) / R_total_cond_conv
	Q_conv = h_out * A_outer * (T_outer - T_ambient)
	Q_rad = epsilon * sigma * A_outer * ((T_outer + 273.15)*4 - (T_ambient + 273.15)*4)
	return Q_cond_conv - (Q_conv + Q_rad)
 
# Solve for T_outer (in °C)
T_outer_guess = (T_coolant + T_ambient) / 2 # Initial guess
T_outer_solution = newton(heat_balance, T_outer_guess)
  
print('''DISCLAIMER: This script was developed by 2 tweakers interested in condensation on insulated PE-X pipes for in-house cooling.
Please only use this if you know what the numbers mean and you know what you are doing.
The writers can and will not take any responsibility for any damages caused by (mis-)use and/or assumptions made based on this''')
 
print(f"Outer Surface Temperature: {T_outer_solution:.2f} °C")
 
Q_cond_conv = (T_coolant - T_outer_solution) / R_total_cond_conv
print(f"Heat transfer per m: {Q_cond_conv:.2f} W")
Q_cond_conv_total = Q_cond_conv * pipe_length
print(f"Total heat transfer : {Q_cond_conv_total:.2f} W")


```