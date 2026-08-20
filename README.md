# AM-radio-receiver
Radio receiver built using an RF module + superheterodyne AM receiver (active bandpass/IF filter, amplifiers, envelope detector)
<br><br>
<img width="1823" height="379" alt="image" src="https://github.com/user-attachments/assets/a3c287bb-3f4c-4388-9c8b-5b2d0a02f7a2" />
<br><br>

## Components
- [IF filter](#if-filter)
- [IF amplifier](#amplifiers)
- [Diode RC envelope detector](#diode-rc-envelope-detector)
- [Audio amplifier](#amplifiers)
<br><br>

## Lab setup
Equipment: EDU33212A function generator, MSO-X 3024T oscilloscope, protoboard

## Diode RC envelope detector
I started off by building an RC circuit (schematic below).
<br><br>
<img width="1851" height="563" alt="image" src="https://github.com/user-attachments/assets/9c6171d1-86be-4ac1-ba04-ee41491f772b" />
<br><br>
In the diagram above, v_in(t) is a 10V pp 800Hz cosine wave, which I displayed on channel 1 of the oscilloscope. 
v_C(t) is the voltage across the capacitor, displayed on channel 2 of the oscilloscope.
<br><br>
**Phase differences**
<br><br>
Using time cursors, I measured the time difference between v_in(t) and v_C(t) to be 170us (170 microseconds), which is a 48.96 degree phase difference.
With a 1kHz cosine wave input, the time difference decreases to 150us, but because of the shortened period, the phase difference actually increases to 54 degrees.
Since the phase difference is positive, v_C(t) is lagging v_in(t).
<br><br>
Voltage across the resistor can be calculated using the subtract operator on the oscilloscope. By KVL, v_R = v_in - v_C. This came out to be about 10V pp.

v_R(t) had a phase shift of approximately -90 degrees, which meant it was leading v_C(t).
<br><br>
**Transient response and time constants**
<br><br>
To measure transient response, the waveform was changed to a 1V pp 100Hz square wave with a 0.5V DC offset. Oscilloscope settings were not changed.
Across the square wave's positive half-period, the capacitor charges. 
During the negative half-period, the capacitor discharges through the resistor starting from voltage v_max and with a time constant RC. 
The voltage over the negative half-period is v_C(t) = v_max * e^(−t/RC).
<br><br>
The time constant can be measured by setting the trigger to "falling edge" on channel 1 and using time cursors to measure the time it takes for v_C to decay to ~37% of v_max.
I measured a time constant of 206us, a 6.36% error compared to the calculated time constant of RC = 220us.
<br><br>
**Frequency filters**
<br><br>
Voltage measured across capacitors and resistors in RC circuits can vary depending on the frequency of the co-sinusoidal input, allowing RC circuits to act as frequency filters and envelope detectors.
Using a 10V pp sine wave input, I recorded the amplitude of the capacitor voltage for different frequencies.
<br><br>
| Frequency | v_C Amplitude (V) |
| ------------- | ------------- |
| 100Hz | 9.95 |
| 500Hz | 8.12 |
| 1kHz | 5.9 |
| 5kHz | 1.7 |
| 10kHz | 1.0 |
| 50kHz | 0.4 |
| 100kHz | 0.3 |

<br><br>
Plotting this data on a logarithmically scaled graph gives an approximately linear relationship.
<br><br>
<img width="1052" height="770" alt="image" src="https://github.com/user-attachments/assets/6cf3c27d-c59f-4784-b8f0-31eac0162584" />
<br><br>
**AM envelope detector**
<br><br>
<img width="531" height="310" alt="image" src="https://github.com/user-attachments/assets/d17e987d-3f66-4488-9186-40a4d47066f7" />
<br><br>
AM radio signals are essentially high-frequency sine waves with amplitudes that are modulated by an information/message signal (such as an audio recording). These message signals are at a low frequency, normally in the audio range of 20Hz to 20kHz. Signals of this frequency cannot propagate well, so they have short range and poor penetration through material obstacles. In order for music from a radio station to reach your car, it needs to modulate onto a high-frequency carrier signal so it can travel further. The peaks of the AM signal form the shape of the message signal, also called the AM signal's envelope. The radio receiver will detect this envelope and recover the original message signal to relay to the listener.
<br><br>
Adding a diode to the RC circuit turns it into an envelope detector.
<br><br>
<img width="1569" height="498" alt="image" src="https://github.com/user-attachments/assets/423b88e9-7923-4cd7-99eb-b6e04383cd47" />
<br><br>
Oscilloscope output drawn here: (V/div: 0.5V, t/div: 100us)
<br><br>
<img width="587" height="479" alt="image" src="https://github.com/user-attachments/assets/1a5140c6-2edb-4c12-aadd-b1eb321163fa" />
<br><br>
When the input voltage reaches the v_on voltage for the diode, the diode starts conducting. The capacitor has more current flowing through it than the resistor that it is in parallel with does, and it charges to its maximum voltage. When voltage drops below v_on, the diode turns off and the capacitor discharges through the resistor, creating a waveform resembling a saw wave. The capacitor voltage waveform traces the peaks of the original voltage signal and does not drop below 0. Cursors measured the frequency of the output (in green) to be 880Hz, the frequency of the message signal.
<br><br>
**Extra notes**
<br><br>
The RC time constant is only relevant when the capacitor is discharging, because as the capacitor is charging, more current is flowing through it than the resistor.
<br><br>
When inverting the input, the output does not invert because of the diode, which requires a threshold voltage v_on before it starts conducting, and which only lets current flow in one direction. Therefore, the charging phase of the capacitor does not match the discharging phase, and the circuit is not linear.
<br><br>
It is unnecessary to account for an unknown initial charge in the capacitor because the capacitor voltage will naturally either increase or decrease to the correct signal level by design.
<br><br>

## Amplifiers
**IF amplifier**
<br><br>
The IF filter uses a LM741 op-amp with +-15V DC sources to create a non-inverting amplifier with a gain of 21. (Schematic below, where R1 = 20k and R2 = 1k)
<br><br>
<img width="667" height="482" alt="image" src="https://github.com/user-attachments/assets/5120807e-ee90-4122-b481-7a3ac026d0bd" />
<br><br>
Applying a 0.5V pp 100Hz sine wave as the input, I measured v_o to be 10.744V. Measured voltage gain is therefore 10.744/0.497 = 21.596.
Op-amp non-idealities and deviations in the actual resistance of the resistors contribute to this difference in theoretical and calculated gain.
<br><br>
**Audio amplifier**
<br><br>
The audio amplifier uses a similar design to the IF amplifier, except with a gain of 2 (R1 = R2 = 1k), and an additional 10k resistor to divert current to ground. Measured gain is v_o/v_i = 1.985/0.980 = 2.0256. (Schematic below)
<br><br>
<img width="583" height="471" alt="image" src="https://github.com/user-attachments/assets/271e458a-9d54-4eb4-9818-679649dccc97" />
<br><br>
**Three-stage circuit**
<br><br>
Now both amplifiers can be placed around the envelope detector like this to form a three-stage circuit:
<br><br>
<img width="1403" height="532" alt="image" src="https://github.com/user-attachments/assets/ee902e9f-b4a8-4f46-bc02-f49c69f37759" />
<br><br>
I placed a 33uF decoupling capacitor between the envelope detector and audio amplifier to remove the DC component from the input signal of the IF amplifier, since a capacitor acts as an open circuit at DC. The 10k resistor also prevents this capacitor from being charged.
<br><br>
In addition, a 33uF capacitor was placed between the ground and -15V rails of the protoboard to filter out voltage fluctuations from the power supply itself.
<br><br>
<img width="647" height="444" alt="image" src="https://github.com/user-attachments/assets/5bbc231e-6760-4407-8186-de768835862f" />
<br><br>
Essentially, the first amplifier amplifies the input signal enough so that the diode in the envelope detector can turn on and off (v_on can be reached). The second amplifier amplifies the recovered message signal enough to drive a loudspeaker connected to it. It also provides a
buffer between the envelope detector and the loudspeaker, preventing the loudspeaker from changing the time constant of
the tuned envelope detector.
<br><br>
To test this circuit, I used a 0.2V pp 13kHz sine wave w/out DC offset, modulated with an AM frequency of 880Hz and depth of 80%. A sketch of the input and output on the oscilloscope is shown below: (V/div for input: 50mV, V/div for output: 1V, t/div: 200us)
<br><br>
<img width="601" height="488" alt="image" src="https://github.com/user-attachments/assets/77c51e23-4972-47b9-984d-064b70540bc1" />
<br><br>
If we sweep the message signal frequency between 100Hz and 2kHz, the envelope output matches AM frequency.
<br><br>
## IF filter
An active bandpass filter can be built using two capacitors and an LM741 op-amp. (Schematic below)
<br><br>
<img width="1159" height="604" alt="image" src="https://github.com/user-attachments/assets/4be6b008-4ea8-467a-a3e4-9633572f8ac4" />
<br><br>
To verify that this is in fact, a bandpass filter, I recorded the amplitude response of the circuit across the frequency rance 1-25kHz.

Starting with a 1kHz 1V pp sine wave, I recorded the magnitude of the phasor voltage gain (|v_o|/|v_i|) at 1kHz intervals and plotted it out on a graph.

Table of values shown below (After 1kHz, only every 3rd value is depicted for brevity. Additional recorded values >25kHz are shown as well.)
<br><br>
| Frequency (kHz) | Amplitude Response |
| ------------- | ------------- |
| 1 | 0.032 |
| 4 | 0.114 |
| 7 | 0.280 |
| 10 | 0.525 |
| 13 | 0.845 |
| 16 | 0.761 |
| 19 | 0.560 |
| 22 | 0.425 |
| 25 | 0.342 |
| 30 | 0.265 |
| 40 | 0.193 |
| 50 | 0.133 |
| 75 | 0.085 |
| 100 | 0.065 |

<br><br>
Graph shown below. (Logarithmic axis)
<br><br>
<img width="1025" height="809" alt="image" src="https://github.com/user-attachments/assets/3d73d6c0-e099-414f-bb68-b63110dddf53" />
<br><br>
Center frequency is 14kHz with with a maximum amplitude response of 0.873.
