# PIR_sensor_module_using_basic_components

I try to make the PIR sensor module without using the commercial IC which has all the functionality such as normal-mode, re-triggering and turned on only at the dark environment. This is very hard to understand the real concept behind each task as well as their implementation. But somehow we managed to proceed with all their required functionalities using transistors, OPAM and basic components. 

### Here is the schematic

Signal conditioning unit

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled.png)

Decision making unit

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%201.png)

### Here is the final PCB after soldered the components.

Front

![IMG_20220507_102800.jpg](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/IMG_20220507_102800.jpg)

Back

![IMG_20220507_102819.jpg](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/IMG_20220507_102819.jpg)

### Appendix

Pyroelectric infrared sensors, IRA series, exhibit high
sensitivity and reliable performance made possible by
Murata's ceramic technology and Hybrid IC technique
expertise developed over many years.

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%202.png)

How does the PIR sensor work
The aim of the PIR sensor is to detect motion in a specific area. As seen in Figure 1: "PIR
sensor", these sensors contain two parts that are sensitive to IR. Since it is motion that is
important, the signal delivered by the sensor reflects different amounts of IR detected by
each part. If both parts "see" the same amount of IR, the sensor detects nothing. But, if one
of the two parts "sees" more or less IR than the other part, the sensor detects something
and the output voltage of the sensor varies.
Figure 2 shows how the output voltage varies when a heat source moves in or out of the
area protected by a sensor.

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%203.png)

The area of the two rectangles that is IR sensitive is small (≈2 mm² for each rectangle).
Clearly, it would be better to increase the IR sensitive area. This can be done in two ways:
- by increasing the number of sensors
- by using a Fresnel lens
The latter method is highly recommended as it is inexpensive and improves IR reception by
the sensor. It multiplies the area visible to the sensor and it focuses reception of IR by the
sensor.

Three-stage architecture for detecting motion
To detect motion, a three-stage architecture based on op-amps is used to condition the
signal of a PIR sensor. This architecture is explained below.

### Stage 1
The first architectural stage amplifies the signal. It cancels the DC part of the signal and
filters the high frequency noise that could lead to false detections. The schematic of this
first architectural stage is shown in Figure 3.

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%204.png)

Figure 3 shows that noise is filtered thanks to the components R1 and C1. The cut-off
frequency is 5 Hz (fhigh1 = 1/(2 x π x R1 x C1)). This application does not need to work at
higher frequencies because usually we are detecting human motion.
The second filter is used to reject the DC part of the signal. R2 and C2 perform a high pass
filter that has a cut-off frequency of 0.6 Hz (flow1 = 1/(2 x π x R2 x C2)).
Since we do not amplify the DC part of the signal, the Vio, which is the input offset voltage
of the op-amp, has no importance in this application.
The stage gain is 53.3 (Gain1 = 1 + R1/R2). This amplification allows a usable signal that is
higher than the noise level. The DC bias of the first architectural stage is determined by the
sensor. To avoid saturation, the gain must not be too big because amplification is made
around this common mode voltage (which is not Vcc/2).
STMicroelectronics’ TSU101 op-amp is a perfect fit for this architectural stage. As well as
having very low consumption, the TSU101 operates with a typical current consumption of
only 600 nA at Vcc = 3.3 V.
The diode, D1, is explained in Section 5: "Power-up".

### Stage 2

Stage 2 is very similar to stage 1 (see Figure 4). It is used to filter and amplify the AC
signal.

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%205.png)

Regarding the filtering function, the low and high cut-off frequencies respectively are 0.6 Hz
(flow2 = 1/(2 x π x R4 x C4)) and 5 Hz (fhigh2 = 1/(2 x π x R3 x C3)). The gain of this stage is
-52.3 (Gain2 = -R3/R4). This gain means that after stage 2 the signal between 0.6 Hz and
5 Hz will have been amplified 2790 times (69 dB).
In stage 2, the op-amp common mode voltage is set by the divider bridge composed of R6,
R7, R8, and R9 to Vcc/2. This allows greater amplification of the AC signal.
As with stage 1, the Vio of the op-amp has no importance since the DC part of the signal is
not amplified. Moreover, there is no constraint on the gain bandwidth product (GBP),
because the op-amp has to have a GBP greater than 2.6 kHz (fhigh2 x gain x 10). A factor of
10 has been used to avoid any limitations by the GBP. Almost all GBP amplifiers fit this
2.6 kHz requirement.
The TSU101 op-amp is very suitable for this architectural stage. Its low consumption is
particularly advantageous. Although it is a nanopower op-amp, its GBP is higher than
2.6 kHz. Note that TSU101 is also available in dual (TSU102) and quad (TSU104) channel
versions to optimize the footprint on board.

### Stage 3

The third and last architectural stage allows the user to perform a window comparator. As a
consequence, the signal is perfectly conditioned to go to a microcontroller. Figure 5 shows
the schematic of this stage.

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%206.png)

When a heat source is detected, the output of the op-amp U3 and/or U4 is in low state. The
divider bridge, composed of the resistors R6, R7, R8, and R9, is used to set the voltage
reference of these devices. The TSU101 op-amp is an input/output rail to rail op-amp, and
there is no constraint on the input common mode voltage. Thus, while the voltage
references of U3 and U4 are within the Vcc range the window comparator works.
For the following example, the reference of U3 is set to 0.84 * Vcc:

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%207.png)

When the signal (Vout2) is above this reference (2.77 V if Vcc = 3.3 V), the output of U3 is
in low state, close to ground.
U4 is used to detect when the signal is below a certain reference. In this example, the
voltage reference is set to 530 mV thanks to the divider bridge composed of R6, R7, R8, and
R9. The voltage reference calculation is:

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%208.png)

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%209.png)

window comparator

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%2010.png)

![Untitled](PIR%20sensor%20730a55f29c704a03810c4419493b57ff/Untitled%2011.png)

offset null procedure

[https://www.youtube.com/watch?v=5ExpeE_YOwQ](https://www.youtube.com/watch?v=5ExpeE_YOwQ)

[https://www.youtube.com/watch?v=Ut-m8dl7INA](https://www.youtube.com/watch?v=Ut-m8dl7INA)
