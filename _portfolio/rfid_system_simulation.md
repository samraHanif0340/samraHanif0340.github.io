---
title: "RFID System Simulation"
excerpt: "Simulation of end-to-end RFID system"
mathjax: true
header:
  teaser: assets/images/rfid_01_adaptive_IIR_filter.jpg
  
---

{% include feature_row %}

{% include toc %}


## Info
We will describe how to simulate the wireless communication chain of a phase-shift keyed (PSK) modulated radio freqeuncy identification (RFID) system in Matlab. We will endeavour to provide the mathematical equations and corresponding Matlab code to model the transmitter, wireless channel (including transmitter and receiver antenna), and a digital baseband receiver of a RFID system. 

## Repository
The [repository](https://github.com/Adaickalavan/RFID-System-Simulation) consists of the following:  
* Matlab Codes for simulation of RFID system

The project structure is as follows:
```
RFID-System-Simulation        
├── PSKSim.m           -- main file consisting of all simulation parameters
├── constellation.m    -- defines the PSK constelation map and bit encoding
├── channel.m          -- computes antenna channel model
```

The following tools will be used to execute the simulation:
* Matlab R2015a

{: .notice--success}
All equations, models, and algorithms in this repository were derived by hand, verified, and written in Matlab manually without using any off-the-shelf libraries.

## Model

{: .notice--warning}
More content and explanation will be added in this page as and when time permits. Any questions through email on the simulation is much welcomed.

The main file in this simulation model is named `PSKSim.m`. To begin, simply run this main file with the preset default simulation parameters. It will plot various graphs visualizing the entire signal transmission from the transmitter to the symbol decision stage. 

The default simulation parameters should succesfully detect the start/end of the data packet, equalize the signal, and yield zero decoding bit error. The following will be printed in the Matlab command window,
```
Error check
Num of symb err: 0
```
signifying succesful completion of simulation.

### Transmitter
The transmitter simulates `M`-ary PSK signal at carrier frequency `fc` and at data rate of `fc/4`. The `constellation.m` file defines the PSK constellation map on the complex plane and symbol bit encoding. Differential encoding is used to counter cycle slips.
![pic1](/assets/images/rfid_01_constellation_map.jpg){:height="80%" width="55%" .align-center}

The transmitter generates signal of the form:
- initial silence period (unmodulated sinusoidal signal)   
- preamble or header (signifying the start of data packet)
- `N` differentially encoded PSK symbols (payload data)
- end of packet symbol sequence (signifying end of data packet)
- final silence period (unmodulated sinusoidal signal)

NRZ (non-return to zero) pulse shaping is used.

Default simulation parameters for the transmitter are:
```matlab
N = 800; %Number of symbols transmitted
M = 16; %M-ary PSK order
fc = 13.56e6; %Carrier frequency, units: Hz
dataRate = fc/4; %Symbol rate or Baud rate
plotTrue = 1; %enable plot
nSampSim = 32; %Number of samples per symbol for simulating transmitter and channel
nSamp = 2; %Number of samples per symbol for simulating receiver
TsSim = 1/(nSampSim*dataRate); %Sample time for simulating transmitter and channel
Ts = 1/(nSamp*dataRate); %Sample time for simulating receiver
```
Note that the simulation involves multirate sampling. The transmitter and wireless channel are simulated at 16x faster at `TsSim` compared to the digital baseband receiver which is simulated at `Ts`. 

### Wireless Channel & Antenna Model
Wireless channel consists of trasmitter antenna, wireless transmission medium of air, and receiver antenna. For all practical purposes, given that the separation between the transmitter and receiver is normally less than 10cm in an RFID application, we can model the wireless channel as mutually coupled pair of transmitter and receiver antenna coils. 

<!-- Mutual inductance, flux linkage, and circuit equations are described by Karris[^1] in chapter 8.     

computes a seriesRLC-M-parallelRLC antenna channel model

Map bandpass channel transfer function into its lowpass equivalent. Method 1, follows the method described by Jeruchim[^2].

Method 2, follows the method described by Farhang[^3]. Example of Matlab implementation is given on page 364 of the book by line $c=c.^*\exp(-j2\pi[0:length(c)-1]'\times T_{s}f_{c})$ where $c$ is the channel, $T_s$ is the sample time, and $f_c$ is the carrier frequency.


`k` is coupling factor
`Qr` is quality factor of 
`Qc` is quality factor of 
`fres` is the resonance frequency of 
`fres` is the resonance frequency of 


Default simulation parameters for the wireless channel are:
```
k = 0.3; % 0<=k<=1; k is usually comprised between 0.03 and 0.3
Qr = 4;  % Quality factor of receiver antenna
Qc = 3;  % Quality factor of 
fresr = 13.56e6;
fresc = 14e6;
```


### Phase Lock Loop
### Digital Baseband Receiver


 the preamble is used as training sequence by the equalizer) -->


### References
[^1]: S.T. Karris, *Circuit Analysis II with Matlab Applications,* 2nd ed. Fremont California: Orchard Publications, ch. 8, 2003.

[^2]: M.C. Jeruchim, P. Balaban, K.S. Shanmugam, *Simulation of Communication Systems,* 2nd ed. New York: Kluwer Academic Publishers, pp. 143, 2000.

[^3]: B. Farhang-Boroujeny, *Signal Processing Techniques for Software Radios,* 2nd ed. Lulu Publishing House, pp. 50, 2010.