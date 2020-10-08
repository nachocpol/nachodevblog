+++ 
draft = false
date = 2020-10-04T13:09:31+01:00
title = "TEPREL-1B"
description = ""
tags = ["rocketry","engine","math"]
+++

![TEPREL-B Hot test](https://pbs.twimg.com/media/Egw3QCEWoAAabtR?format=jpg&name=large)

TEPREL-1B is a liquid bi-propellant rocket engine developed by PLD Space, a Spanish company working towards a slot in the micro satellite launchers. This engine will be used in MIURA-1 a suborbital vehicle that will be able to carry a 100kg payload to 153km and also serve as a testbed for several technologies. The available specs of the engine are as follows:

- **Propellants**: Liquid oxigen (LOX) & Rocket grade kerosne (RP1)
- **Engine cycle**: Pressure-fed  (helium as pressurant)
- **Thrust (sea level)**: 30kN
- **Burn time**: 122 seconds

On top of this, we also know that the vehicle carries **1000l of LOX** and **600l of RP1**  [1][2]


## Extended specs

Lets figure out additional specs of this engine! Most of the equations that I use here come or are derived from **Rocket Propulsion Elements** [3], an excellent book that goes over the ins and outs of rocket propulsion. Take all these results with a grain of salt, real values may be different from these results.

#### Mass flow rate

Because we know how much propellants the vehicle carries and the burn duration, we can easily estimate the mass flow rate.

$m_{LOX} = 1000 \cdot 1.141 = 1141 \text{kg}$

Where $1.141$ is the density of LOX. 

$m_{RP1} = 600 \cdot 0.820 = 492 \text{kg}$

Where $0.820$ is the density of RP1.

$\dot{m} =\frac{1141 + 492}{122} = \mathbf{13.38 \text{kg/s}}$. This is probably over estimating the actual value, in reality we would need to account for losses due to evaporation of LOX and they probably won't empty the tanks 100%.

Also, note that the stored oxidiser to fuel ratio is $2.32$, a common value for KEROLOX rocket engines. [4]

#### Exhaust velocity

By having the rocket thrust and mass flow rate, we can figure out the velocity of the exhaust:

$F = \dot{m} V_{e}$

$V_{e} = \frac{F}{\dot{m}}$

$V_{e} = \frac{30000}{13.38} = 2242.15 \text{m/s}$
 
Having this we can also figure out the specific impulse (this is at sea level): $I_{SP}=\frac{V_e}{g_0} = \frac{2241.15}{9.81} = 235s$ 

#### Chamber pressure

To get the chamber pressure we can start with the exhaust velocity formula:

$v_e = \sqrt{\frac{2k}{k-1} RT_1 \left({1-\left({\frac{p_y}{p_1}}\right)^\frac{k-1}{k}}\right)}$

Where $v_e$ is the exhaust velocity we calculated above. $R$ is the gas constant. $T_1$ temperature at the combustion chamber and $p_y$ the exit pressure (for sea level, it would be 0.101MPa[5]).  We can re-arrange the equation to solve for $p_1$:

$\frac{{v_2}^2}{\frac{2k}{k-1}RT_1} = 1 - \left({{\frac{p_y}{p_1}}}\right)^{\frac{k-1}{k}}$

I've used [WolframAlpha to resolve the equation](https://www.wolframalpha.com/input/?i=%282242%5E2%29%2F%2810.3*1245600%29%3D1-%28101000%2Fx%29%5E%280.19%29). We get that $p_1 = \mathbf{1.38\text{MPa}}$

#### Throat to exit  ratio

Lets now figure out the physical size of the rocket engine. We can start by looking and the ratio of expansion:

$\frac{A_t}{A_e} = \left({\frac{k+1}{2}}\right)^\frac{1}{k-1} \left({\frac{p_e}{p_1}}\right)^\frac{1}{k} \sqrt{\frac{k+1}{k-1}\left(1-\left(\frac{p_e}{p_1}\right)^\frac{k-1}{k}\right)}$

$E=\frac{A_t}{A_e}=0.37$

Now, lets get the area at the throat:

$A_t = \frac{\dot{m}}{P_1}\frac{RT_1}{k\left({\frac{2}{k+1}}\right)^{\frac{k+1}{k-1}}} = \mathbf{0.028m^2}$

$A_2 = \frac{A_t}{0.37} = \mathbf{0.075m^2}$

And if we convert this to a diameter we get that $D_t = \mathbf{18.8cm}$ and $D_2 = \mathbf{30.9cm}$


#### Final stats

And these are the final stats:

- **Specific impulse**: 235s
- **Mass flow rate**: 13kg/s
- **Exhaust velocity**: 2242m/s
- **Chamber presure**: 1.38MPa
- **Throat diameter**: 19cm
- **Nozzle exit diamter**: 31cm

#### References

[1] https://www.pldspace.com/es/miura-1

[2] https://www.pldspace.com/es/documentos

[3] http://mae-nas.eng.usu.edu/MAE_5540_Web/propulsion_systems/subpages/Rocket_Propulsion_Elements.pdf

[4] https://en.wikipedia.org/wiki/RP-1

[5] https://en.wikipedia.org/wiki/Atmospheric_pressure#Mean_sea-level_pressure


![TEPREL-A](https://pbs.twimg.com/media/DD9YX0AWsAANPEh?format=jpg&name=large)

<!-- 
Where $p_a$ is the ambient pressure, $k$ is the ratio of specific heats (1.23 is a common value for KEROLOX) and $M$ is the exit gas mach number.

$T_t=T_c \frac{2}{k+1}$

$c=\sqrt{kRT}$

$p_c = p_a\left({1+ \frac{1}{2} \left({k-1}\right) M^2 }\right)^{\frac{k}{k-1}}$
-->
