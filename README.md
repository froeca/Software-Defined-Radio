# Cheap Software Defined Radio Project

This repository covers the design of a simple, low-cost software defined radio (SDR) receiver to be used in conjunction with Quisk, a free open source SDR software. Quisk is compatible with Python and is hosted on the PyPi repository. More information on Quisk can be found here: http://james.ahlstrom.name/quisk/docs.html. Most of the discussion following will focus on the hardware design, construction, and testing of the radio receiver. Block diagrams, schematics, and other files are available and are openly licensed. Feel free to contact me with any questions.

## Theory and Introduction

The goal of software defined radios is to shift the focus of radio communication from analog devices to digital solutions. By taking components that have been traditionally implemented in hardware and implementing them by means of software on a personal computer or embedded system, an SDR offers several benefits including cheaper and simpler radios and ability to alter functionality at will. 

(more theory)

The design objectives given to us by our Professor, Dr. Rob Frohne are listed below:

### Design Objectives:
1. Minimum discernible signal less than 1 uV.
2. Good image rejection.
3. Low noise figure.
4. Inexpensive to build.

My project partner, Konrad McClure, and I, also set several design objectives of our own when designing our radio receiver. They are as follows:

- Simple design: We wanted our receiver design to be as straightforward as possible. We avoided using a transformer, and chose to use Dan Tayloe’s mixer design since it was simplistic and easy for us to understand.
-	Ease of construction: Since we would be building these board in our homes, without all the tools available to us in the lab, we sought to minimize the amount of work necessary to construct our boards. This influenced many aspects of our design process: our part selection, our PCB layout, and our testing procedures.
- Ease and quality of amplification:

We designed our reciever to operate in the (5.000 - 10.000 MHz) band. This band was chosen because it is centered around the "40-meter" Ametuar Radio band which supports communication worldwide and is considered one of the most reliable all-season long distance communication bands. We also noticed by experimenting with an online SDR radio that there seemed to be a substantial number of stations avalible in that band. For simplicity sake, we kept the design to just one receiving freqeuncy band.

We choose our intermediate frequency (IF) to be 10.7kHz. This is a standard IF value.

## Hardware
A block diagram of our SDR reciever is shown below in Figure 1:

(Insert image)

The design work is split into five sections namely;  bandpass filter (orange), Tayloe mixer (green), low-pass filter (orange), local oscillator (red) and audio section (blue). The five sections will be discussed below.

## Bandpass Filter

By cascading a low-pass filter with a high-pass filter we can form a passive filter that passes a “band” of frequencies. We chose to use LC filters. This will help minimize the noise injected into our circuit. Due to the way electrons move within the resistor, signal noise is predominantly caused by high value resistors. Thus, we chose to avoid RL and RC filters. A simple block diagram for our bandpass filter is seen below:

(Insert image)

The passband for our filter is 5.000-10.000 MHz. Thus, defining our filter design parameters we have:

-	Bandwidth:  	 		bw = 5.000 MHz.
-	Center frequency:		fc = 7.500 MHz
-	Upper cut-off frequency:	f1 = 10.000 MHz
-	Lower cut-off frequency:	f2 = 5.000 MHz
-	Input impedance:		Zin = 50 Ω
-	Output impedance:		Zout = 50 Ω

To get a good steep slope on the Bode magnitude plot while keeping the cost relatively low, we chose to use a 3rd order bandpass filter. Initially, we selected a series-first Butterworth filter topology due to the Butterworth’s maximally flat response and insensitivity to slight changes in component values. However, since we were planning to run our IC’s on a single supply, we needed to provide a DC offset to the RF input. One way to accomplish this is by means of a transformer, however, the simpler solution, or so we thought, was to switch filter topologies. Using a series-first Bessel filter allowed us to use a voltage divider to generate our DC bias point.

The bandpass filter with ideal component values is seen below. 

(Insert image)

Our filter design from the schematic (v3a) that we built is seen below with the actual component values. Somehow, our component values got messed up in the throes of design, testing, and purchasing of parts. This issue has been fixed in our updated schematic (v3b). The difference between our filter and the designed filter, can be visualized in XXXXX.

(Insert image)

To keep costs low, we are using 10% capacitors and hand-wound toroidal inductors. Information on what toroid cores we are using and the number of turns necessary to get the specified inductance is shown below:

Ref:	L (nH)	Toroid:	AL (nH/turn2)	# of turns:
L1	200	T37	3	8.165
L2	1300	T50	4	18.028
L3	1300	T50	4	18.028

## Tayloe Mixer

For our quadrature mixer, we chose to follow the design outlined in Dan Tayloe’s paper on his Tayloe detector (“Ultra Low Noise, High Performance, Zero IF Quadrature Product Detector and Preamplifier.”) We appreciated the Tayloe mixer design due to its simplicity and excellent performance. The basic structure of a Tayloe mixer can be seen below. 

(Insert photo)

The Tayloe mixer is essentially a four-position switch that cycles at the local oscillator frequency. Sampling capacitors are placed such that the signal is sampled four times each period of the RF. Differentially summing the outputs produces the I and Q signals at the intermediate frequency. Expressing this mathematically, the output frequency of the I and Q signals is equal to the difference between the RF frequency and ¼ of the local oscillator frequency. 

f_{IF}=f_{RF}-\frac{1}{4}f_{LO}

Since we are aiming for an intermediate frequency of fIF = 11.7 kHz, with 5.000 MHz < fRF < 10.000 MHz, the local oscillator frequency will roughly be four times the RF frequency. Thus, maximally fLO will be about 40MHz. At this frequency, it is imperative that switching delays be minimized. We chose to use a fast-switching multiplexer (SN74CBT3253) in our design. The propagation delay of the SN74CBT3253 is maximally:    tpd = 6.6 ns   at a supply voltage of 4.0 V. This is considerably less than the period of a 40 MHz signal (25 ns). Thus, the SN74CBT3253 satisfied our need for a high-speed switch. 

The SN74CBT3253 were also attractive for their low on-state resistance (ron = 7 Ω max), and their cheap price ($0.58) and availability on Digi-Key. Unfortunately, the SN74CBT3253 is a 16-SOIC surface-mount package type which made hand soldering to our PCB more difficult. 
Each sampling capacitor form an RC low-pass filter with the 50 Ω output impedance of the bandpass filter. Our capacitor values were chosen based on simulation and testing done in LTSPICE. 

Below is our Tayloe mixer design. It consists of the SN74CBT3253, 1:4 FET multiplexer that switches the signal to each of the four sampling capacitors.

(Insert image)

## Instrumentation Amplifiers:

To differentially sum the signals sampled from the Tayloe mixer we chose to use an instrumentation amplifier. This was done for three reasons: 

1.	Eliminate gain variance: During our simulations with a normal op-amp we noticed that the gain deviated from what we had designed it for. The issue we faced was that the gain of the circuit was affected negatively by the input impedance seen through the Tayloe mixer. To avoid this issue, we chose to use an instrumentation amplifier which the gain via an internal resistor.
2.	Reduce common-mode gain: Instrumentation amp
3.	Have more control over the gain:  Since the gain resistor is internal, we were able to simply add jumpers in parallel with our gain resistor, allowing the ability to increase/reduce the gain relatively easily. 

After much research, we settled on the INA821ID amplifiers. They suit the needs of our circuit quite well. The advantages we noticed were:

-	Low noise:  				7 nV/√Hz
-	Compatible with single supply: 		4.5 V to 36 V
-	High common mode rejection ratio:  	CMMR > 92 dB

The price ($5.15) was considerably less than other low-noise instrumentation amplifiers available on Digi-Key. A downside of the INA821IDs was the fact that they were surface mount parts. 

Below is the schematic for our instrumentation amplifiers:

(Insert image)

## Local Oscillator:

For our local oscillator, we chose to use the SI5351-AB-GT clock generator due to Professor Frohne’s familiarity with the device and the availability of software. While the SI5351-AB-GT was a nice cheap option, its insanely small size and surface mount package type made the clock generator incredibly hard to hand solder. 

The SI5351-AB-GT is driven by an external crystal. Per the datasheet, the crystal must have a frequency of 25 or 27 MHz, and a load capacitance anywhere between 6-12 pF. We chose to use the ATS250 crystal primarily due to its low cost ($0.37) and availability on Digi-Key. 

A divide by four Johnson counter was used to control the select lines of the mux. The 74AC74 D-latches were selected based on their low propagation delay ( >10.5 ns typical ) and their familiar through-hole package. 

Below is a schematic of our local oscillator digital circuitry:

(insert image)

## Audio Section: 

