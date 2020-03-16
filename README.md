# Bicycle dynamo specificator

This setup was built to specify the output voltage trace and power of a bicycle dynamo with different loads attached. 
This was done to see if it is feasible to charge a phone from a bicycle at standard USB specifications (5V@500mA=2.5W) and to evaluate what circuitry options would be viable.


## Setup
The setup consists of an e-bike wheel directly attached to a bicycle dynamo:
![Setup](https://github.com/petl/Bicycle_dynamo_specificator/blob/master/setup.jpg)
The dynamo was bought from Amazon: ['AXA-BASTA "HR Traction"'](https://www.amazon.de/gp/product/B001F06TQI/). Since it is technically a single phase permanently exited AC motor, the rotation speed can easily be calculated with the number of pole pairs, the outer diameter of the rotor and the frequency of the waveform. 

Different load resistors (Rload) were connected to get the load specifications of the dynamo. Rload was a know resistance, the voltage was measured with a scope (Rigol 1054Z) and second order low pass (LP) filtered (fcut=1kHz). The momentary power was calculated and then averaged over the sample time. 

![Equivalent measurement schematic](https://github.com/petl/Bicycle_dynamo_specificator/blob/master/Measurement_schematic.png)

## Script

The [script](https://github.com/petl/Bicycle_dynamo_specificator/blob/master/GetDataFromRigol1054Z.ipynb) written in python and run in an [jupyter notebook](https://jupyter.org/) is composed of three parts:

### Scope

The Rigol 1054Z was used with a [library](https://pypi.org/project/ds1054z/) to wrap the SCPI commands. This enabled pulling waveform out of the scope in 3 lines possible. 
Output:
> Connected to:  RIGOL TECHNOLOGIES,DS1104Z,DS1ZAXXXX,00.04.04.SP3

> Currently displayed channels:  ['CHAN1']

> got datapoints: 1200


### Filter & Calc

We take the waveform, calculate the sample period and sample rate. This is used for the LP filtered voltage trace (wave), to get the speed of the dynamo and to calculate the output power with the known resistance. 
Output: 
> Wheel speed: 30.563 km/h

> Average power over resistor: 6.14W

### Plot

A pyplot throws all the data into a plot, including the speed, resistance and powerAVG as a textbox and additionally saves the raw data as csv to be edited later. 



## Results
To get to the results quickly, here's the power output plot with different Rload over speed:

![powerVSSpeed_all_resistances](https://github.com/petl/Bicycle_dynamo_specificator/blob/master/Measurements/powerVSspeed_all_resistances.png)

The critical 2.5W point is firstly reached by the 4 Ohm load at around 16km/h(9.94mph), but the 10 Ohm load surpasses it at around 18km/h (11.18mph). A maximum power point tracking (MPPT) circuitry would therefore be adviseable to cover the low and high speed maximum output capabilities of the dynamo. Since the theoretically achieveable output power is be directly proportional to the speed which can be directly calculated with the output frequency the implementation of MPPT could be done in software with a few lines of code. Alternatively a MPPT IC, although being made for photovoltaik applications, should work. 

![dynamo_18kmh_10Ohm](https://github.com/petl/Bicycle_dynamo_specificator/blob/master/Measurements/Dynamo_18.0kmh_10Ohm.png)

The output voltage waveform is sinusoidal with a frequency of ~375Hz and a peak voltage of 7.45V (14.9V peak-to-peak) at 18km/h(11.18mph) drive speed. The rectification could be done by (Schottky) diodes or an active rectifcation. A quick calculation shows that a full bridge Schottky rectifier would waste around 2\*0.3V\*0.5A=0.3W of power, which is roughly 10% of the total power budget. The active rectification for example with a [LT4320-1](https://www.analog.com/media/en/technical-documentation/data-sheets/4320fb.pdf) should therefore be prefered.   




Uploaded data, plots and script from the measurement of a bicycle dynamo 
