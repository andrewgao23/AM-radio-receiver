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
Center frequency is 14kHz with with a maximum amplitude response of 0.873. 3dB cutoff frequencies are at 11kHz and 18kHz, both with amplitude responses of 0.615. This circuit thus has a 3dB bandwidth of 7kHz and a quality factor of 14kHz/7kHz = 2.
<br><br>

## Frequency Domain

Before the message signal is multiplied by the high-frequency carrier signal, a DC component is added so that the voltage always stays above 0, making the message signal easier to recover from the carrier envelope.
<br><br>
In order to test various points in the superheterodyne receiver, I first practiced displaying a synthesized AM signal in the frequency domain.
<br><br>
1V pp, 14kHz sine wave modulated w/ 880Hz sine wave with 80% modulation depth (adjusts DC component added to message signal). Sketch of oscillscope FFT below (time/div = 1ms, Volt/div = 500mV, frequency span = 20kHz, scale = 15dB/div)
<br><br>
<img width="838" height="672" alt="image" src="https://github.com/user-attachments/assets/5eb12e09-9f64-4583-abfb-32ef21a71b04" />
<br><br>
Through the modulation property Y(w) = 0.5F(w-w_0) + 0.5F(w+w_0), the carrier cosine function shifts the baseband message before modulation by +- 880Hz, which adds two peaks in the FFT graph: one at 14kHz - 880Hz = 13.12kHz, and one at 14.88kHz.
<br><br>
With a square message signal, oscilloscope FFT changes to this (settings do not change):
<br><br>
<img width="826" height="670" alt="image" src="https://github.com/user-attachments/assets/bfd2afcd-722e-4403-8e50-c7046fe6dc09" />
<br><br>
The spectrum shows multiple pairs of lines symmetrically placed around the 14kHz carrier, decreasing in amplitude with harmonic order. 
<br><br>

## Testing

Connecting the function generator to the local oscillator input on the frequency mixer, I tuned WILL 580 (580kHz) by selecting an appropriate mixing frequency with 100 mV pp as the local oscillator so that the intermediate frequency is 14kHz.
<br><br>
**Test Point 1: RF Amplifier**
<br><br>
(time/div = 500us, span = 400kHz, scale = 20dBV, Volt/div = 10mV)
<br><br>
<img width="607" height="493" alt="image" src="https://github.com/user-attachments/assets/7d8f211a-a9ff-4c9b-94c1-d13b8d181e02" />
<br><br>

**Test Point 2: Frequency Mixer**
<br><br>
(time/div = 1ms, span = 28kHz, scale = 20dBV, Volt/div = 200mV)
<br><br>
<img width="528" height="430" alt="image" src="https://github.com/user-attachments/assets/ba8562fb-c4b6-4ac6-8a38-dd538039e78f" />
<br><br>

**Test Point 3: IF Filter & Amplifier**
<br><br>
(time/div = 50us, span = 28kHz, scale = 20dBV, Volt/div = 200mV)
<br><br>
<img width="531" height="432" alt="image" src="https://github.com/user-attachments/assets/215e23b6-9146-447e-9588-ead54788ed09" />
<br><br>

**Test Point 4: Envelope Detector & Audio Amplifier**
<br><br>
Hooking up the output of the audio amplifier to the speaker and tuning to radio station WILL 580 kHz yields audible sound outputs from the speaker.
<br><br>

## Software Radio
A software radio with the same specifications as the analog receiver circuit will allow analysis between continuous-time system outputs and discrete-time signal processing (DSP). The conversion of a continuous-time input signal to a discrete-time signal is called sampling (or A/D conversion), and the conversion of a discrete-time signal to a continuous-time output signal is called reconstruction (or D/A conversion). Samples f(nT) of a band-limited analog signal f(t) can be used to reconstruct f(t) exactly when the sampling interval T and signal bandwidth Ω = 2πB satisfy the Nyquist criterion T < 1/(2B).
<br><br>
**Synthetic AM signal processed using softRx**
<br><br>
For this, I used MATLAB/Simulink with a softRx program to compare continuous-time and frequency domains. First I generated a 13kHz 250mV pp sine wave modulated w/ 880Hz message signal at 80% modulation depth.
<br><br>
<img width="877" height="717" alt="image" src="https://github.com/user-attachments/assets/29459775-eb47-4071-9432-12271746698e" />
<br><br>
The strong impulse at 13kHz in the frequency domain represents the carrier frequency of the AM signal. The other two impulses surrounding the 13kHz impulse are the sidebands, located at 13kHz +- 880Hz. This represents the modulation property in Y(w)(0.5F(w+w_0)+0.5F(w-w_0)).
<br><br>
Next I simulated an IF bandpass filter in softRx and sketched the frequency domain output.
<br><br>
<img width="877" height="402" alt="image" src="https://github.com/user-attachments/assets/05e91e43-fcaf-438c-82cc-5479fbdbc928" />
<br><br>
Adding an envelope detector to the simulation yields the following outputs in frequency and time domains respectively:
<br><br>
<img width="812" height="682" alt="image" src="https://github.com/user-attachments/assets/94412843-2fab-46c6-b6a8-b83930dad278" />
<br><br>
In the time domain, we see a somewhat sinusoidal signal, while in the frequency domain, we see a dominant signal at 880Hz with a 40-50dB amplitude, which is much more than the other harmonic signals. The envelope detector recovered the original message signal from the IF AM signal centered at 13kHz.
<br><br>
Block diagram for the software simulation of the AM receiver (IF filter only, IF filter + envelope detector, respectively):
<br><br>
<img width="1231" height="225" alt="image" src="https://github.com/user-attachments/assets/0e20b017-e71c-4857-9104-4a36d2454ad0" />
<img width="1397" height="220" alt="image" src="https://github.com/user-attachments/assets/2d919561-020f-42dc-bfa2-c604b9e018b5" />
<br><br>
**Receiving broadcast AM**
<br><br>
With a 13kHz IF, local oscillator frequencies must be +- 13kHz with respect to the radio station frequencies. (566kHz or 593kHz when tuning to AM 580, and 1387kHz or 1413kHz when tuning to AM 1400)
