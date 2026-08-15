# AM-radio-receiver
Radio receiver built using an RF module + superheterodyne AM receiver (active bandpass/IF filter, amplifiers, envelope detector)
<br><br>
<img width="1823" height="379" alt="image" src="https://github.com/user-attachments/assets/a3c287bb-3f4c-4388-9c8b-5b2d0a02f7a2" />
<br><br>

## Components
- [IF filter](if-filter)
- [IF amplifier](if-amplifier)
- [Diode RC envelope detector](diode-rc-envelope-detector)
- [Audio amplifier](audio-amplifier)
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
