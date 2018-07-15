---
title: "RFID System Simulation"
excerpt: "Simulation of end-to-end RFID system"
header:
  teaser: assets/images/rfid_00_adaptive_IIR_filter.jpg
mathjax: true
gallery_01:
  - url: /assets/images/rfid_02_seriesRLC.jpg
    image_path: /assets/images/rfid_02_seriesRLC.jpg
  - url: /assets/images/rfid_03_parallelRLC.jpg
    image_path: /assets/images/rfid_03_parallelRLC.jpg
  - url: /assets/images/rfid_03_parallelRLC.jpg
    image_path: /assets/images/rfid_03_parallelRLC.jpg

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
├── PSKSim.m           # main file consisting of all simulation parameters
├── constellation.m    # defines the PSK constelation map and bit encoding
├── channel.m          # computes antenna channel model
├── ADPLL_degree.m     # implements phase lock loop
└── Subaxis            # helper module for plotting
    ├── parseArgs.m    # helper function for parsing varargin
    └── subaxis.m      # create axes in tiled positions
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
<div style="text-align: center">
  <a href="/assets/images/rfid_01_constellation_map.jpg"><img src="/assets/images/rfid_01_constellation_map.jpg" height="40%" width="40%"></a>
  <figcaption>Fig. 16-ary PSK constellation points on the unit circle, plus one end-of-packet symbol at coordinate $(-1, 0)$</figcaption>
</div>

The transmitter generates signal of the form:
- initial silence period (unmodulated sinusoidal signal)   
- preamble or header (signifying the start of data packet)
- `N` differentially encoded PSK symbols (payload data)
- end of packet symbol sequence (signifying end of data packet)
- final silence period (unmodulated sinusoidal signal)

NRZ (non-return to zero) pulse shaping is used.

Default simulation parameters for the transmitter are:
```matlab
N = 800;                       % Number of symbols transmitted
M = 16;                        % M-ary PSK order
fc = 13.56e6;                  % Carrier frequency, units: Hz
dataRate = fc/4;               % Symbol rate or Baud rate
plotTrue = 1;                  % Enable plot
nSampSim = 32;                 % Number of samples per symbol for simulating transmitter and channel
nSamp = 2;                     % Number of samples per symbol for simulating receiver
TsSim = 1/(nSampSim*dataRate); % Sample time for simulating transmitter and channel
Ts = 1/(nSamp*dataRate);       % Sample time for simulating receiver
```
Note that the simulation involves multirate sampling. The transmitter and wireless channel are simulated at 16x faster at `TsSim` compared to the digital baseband receiver which is simulated at `Ts`. 

### Wireless Channel & Antenna Model
Wireless channel consists of trasmitter antenna, wireless transmission medium of air, and receiver antenna. The transmitter and receiver antenna circuit is modelled as series and parallel RLC circuit, respectively. Refer to Karris[^1] chapter 2, for detailed derivation of each individual circuit transfer function and antenna quality factor $Q$. 

For all practical purposes, given that the separation between the transmitter and receiver is normally less than 10cm in an RFID application, we can model the wireless channel as mutually coupled pair of transmitter and receiver antenna coils. This leads to an equivalent circuit where the inductors are coupled via mutual inductance $M=k\sqrt{L_t\times L_r}$. Here, $k$ is the coefficient of coupling and provides a measure of the proximity of the primary
and secondary coils, with values typically between 0.03 and 0.3. The value of $k$ is exactly unity only when the two coils are coalesced into a single coil.
<figure>
  <a href="/assets/images/rfid_02_equivalent_circuit.jpg"><img src="/assets/images/rfid_02_equivalent_circuit.jpg"></a>
  <figcaption>Fig. Antenna channel model. (Left): individual seriesRLC-$M$-parallelRLC circuits. (Right): Equivalent circuit</figcaption>
</figure>
The overall Laplace $s$ domain bandpass transfer function of combined resonantor channel is given as

$$
\begin{align}
\frac{C_1MR_2s^2}{\left[
\begin{array}{c}
R_2 + L_2s - C_1M^2s^3 + C_1L_1L_2s^3 + C_1L_1R_2s^2 + C_1L_2R_1s^2 + C_2L_2R_2s^2 +
\\
C_1R_1R_2s - C_1C_2M^2R_2s^4 + C_1C_2L_1L_2R_2s^4 + C_1C_2L_2R_1R_2s^3
\end{array}
\right]}
\end{align}
$$

Symbolic derivation of the transfer function of resonator channel is shown on lines 7 - 20 in `channel.m` file. Mutual inductance, flux linkage, and equivalent circuit equations are further elaborated by Karris[^1] in chapter 8.     

The analog bandpass channel transfer function is then mapped to digital lowpass equivalent. There are two methods to achieve this.

Method 1, follows the procedures described by Jeruchim[^2], page 143. Poles located on the negative-frequency half of $s$-plane are discarded and poles located on the positive-frequency half $s$-plane is shifted to the zero axis by substituting $s \rightarrow s+jw$. The transfer function is then discretized from $s$ domain to discrete $z$ domain. 

Method 2, follows the procedures described by Farhang[^3], page 50. First, discretize the Laplace domain transfer function to obtain discretized transfer function. Then, substitute $z^x \rightarrow z^x\exp\left(j2\pi f_cT_sx\right)$ in the z-domain transfer function of the passband channel. Here, $f_c$ is the carrier frequency to donwconvert and $T_s$ is the sampling time. Example of Matlab implementation is given on page 364 of the book by line $c=c.^\star\exp(-j2\pi[0:length(c)-1]'\times T_{s}f_{c})$ where $c$ is the channel, $T_s$ is the sample time, and $f_c$ is the carrier frequency.

Both methods are implemented in `channel.m` file and visualized in the following figure.
<figure>
  <a href="/assets/images/rfid_03_channel_characteristic.jpg"><img src="/assets/images/rfid_03_channel_characteristic.jpg"></a>
  <figcaption>Fig. (TopLeft): Laplace domain equivalent channel pole-zero map. (TopRight): Discrete domain equivalent channel pole-zero map. (Bottom): Frequency response of equivalent channel</figcaption>
</figure>

Frequency response of method 1 and method 2 around the zero frequency region (i.e., region of interest) is similar and matches that of Laplace $s$ domain equivalent lowpass transfer function. 

The received modulated passband waveshape at the receiver after propagating through the transmitter and wireless channel is depicted below.
<div style="text-align: center">
  <a href="/assets/images/rfid_04_passband_waveshape.jpg"><img src="/assets/images/rfid_04_passband_waveshape.jpg" height="80%" width="80%"></a>
  <figcaption>Fig. Passband waveshape received at the receiver</figcaption>
</div>

Default simulation parameters for the wireless channel and antenna model are:
```
k = 0.3;         % 0<=k<=1; k is usually comprised between 0.03 and 0.3
Qr = 4;          % Quality factor of transmitter antenna
Qc = 3;          % Quality factor of receiver antenna
fresr = 13.56e6; % Resonance frequency of transmitter circuit
fresc = 14e6;    % Resonance frequency of receiver circuit
```

### Phase Lock Loop

The phase of signal is extracted using a phase lock loop at the analog front-end of the receiver. Read [^4] & [^5].


### Digital Baseband Receiver

Matched filtering is used to detect the start of the data packet. The preamble of the data packet is used as training sequence by the equalizer.

Affine projection algorithm is utilised to equalize the dispersed signal. Affine projection is chosen due to its favourable properties:
- It has a complexity in-between least-mean square(LMS) and recursive least squares(RLS) algorithm.
- It provides ability to control the adaptation-speed, -magnitude, and misadjustment of the coefficients.
<div style="text-align: center">
  <a href="/assets/images/rfid_08_affine_projection.jpg"><img src="/assets/images/rfid_08_affine_projection.jpg" height="55%" width="55%"></a>
  <figcaption>Fig. Baseband receiver utilizing affine projection equalization structure</figcaption>
</div>

Refer to P.S.R. Diniz[^6], section 4.6 for detailed explanation of the affine projection algorithm, and table 4.5 for a summary of the algorithm steps.

The equalizer is configured to be a decision-feedback fractionally spaced equalizer, as fractionally spaced equalization achieves optimum matched filtering and symbol time synchronisation. A fractionally spaced equalizer outputs a single decision for each symbol sampled at a rate higher than the symbol rate. 

The decoding by the digital baseband receiver is stopped when the end of packet symbol sequence is detected.

Default simulation parameters for the digital baseband receiver model are:
```
kFwd_pos = 0:-1:-2;      % number and position of feed forward inputs of the equalizer
kBwd = 2;                % number of decision feedback inputs of the equalizer
mu = 0.25;               % convergence factor (step) (0 < mu < 1);
L = 2;                   % data reuse factor (L=0 -> NLMS; L=1 -> BNLMS; ...)
gamma = 1;               % small positive constant to avoid singularity
bias = 0;                % default bias value of the equalizer
delay = 1;               % timesteps to be skipped between consecutive input data fed to the affine projection equalizer
fracEn = 1;              % enable fractionaly spaced equalization
```

### References
[^1]: S.T. Karris, *Circuit Analysis II with Matlab Applications,* 2nd ed. Fremont California: Orchard Publications, 2003.

[^2]: M.C. Jeruchim, P. Balaban, K.S. Shanmugam, *Simulation of Communication Systems,* 2nd ed. New York: Kluwer Academic Publishers, pp. 143, 2000.

[^3]: B. Farhang-Boroujeny, *Signal Processing Techniques for Software Radios,* 2nd ed. Lulu Publishing House, pp. 50, 2010.

[^4]:

[^5]:

[^6]: P.S.R. Diniz, *Adaptive Filtering - Algorithms and Practical Implementation,* 4th ed. New York: Springer, pp. 162 - 168, 2013. 