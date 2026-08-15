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
I started off by building an RC circuit:
<br><br>
<img width="1851" height="563" alt="image" src="https://github.com/user-attachments/assets/9c6171d1-86be-4ac1-ba04-ee41491f772b" />
<br><br>
In the diagram above, v_in(t) is a 10V pp 800Hz cosine wave, which I displayed on channel 1 of the oscilloscope. 
v_C(t) is the voltage across the capacitor, displayed on channel 2 of the oscilloscope.
Using time cursors, I measured the time difference between v_in(t) and v_C(t) to be 170us (170 microseconds), which is 48.96 degree phase difference.
With a 1kHz cosine wave input, the time difference decreases to 150us, but because of the shortened period, the phase difference actually increases to 54 degrees.
Since the phase difference is positive, v_C(t) is lagging v_in(t).
Voltage across the resistor can be calculated using the subtract operator on the oscilloscope. By KVL, v_R = v_in - v_C. This came out to be about 10V pp.
v_R(t) had a phase shift of approximately -90 degrees, which meant it was leading v_C(t).
<br><br>
To measure transient response, the waveform was changed to a 1V pp 100Hz square wave with a 0.5V DC offset. Oscilloscope settings were not changed.
Across the square wave's positive half-period, the capacitor charges. 
During the negative half-period, the capacitor discharges through the resistor starting from voltage v_max and with a time constant RC. 
The voltage over the negative half-period is v_C(t) = v_max * e^(−t/RC).
<br><br>
The time constant can be measured by setting the trigger to "falling edge" on channel 1 and using time cursors to measure the time it takes for v_C to decay to ~37% of v_max.
I measured a time constant of 206us, a 6.36% error compared to the calculated time constant of RC = 220us.
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
