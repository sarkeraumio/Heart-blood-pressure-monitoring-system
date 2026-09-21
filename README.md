# Heart & Blood Pressure Monitoring System (BBC micro:bit)

An automatic, cuff-based blood pressure monitor built on the BBC micro:bit. A motor-driven pump inflates an arm cuff, a pressure sensor feeds the micro:bit's ADC, and the oscillometric envelope is processed on-device to estimate **systolic**, **diastolic** and **pulse rate**. Results are shown on an OLED display and can be transmitted to a second micro:bit over the built-in 2.4 GHz radio.

This repository also contains the supporting lab exercises (Lab 1 and Lab 2) that build up the individual skills used in the final system — LED/light control, audio output, OLED graphics, and voltage/current/resistance measurement.

## Demo

📺 **[Watch the full project playlist on YouTube](https://www.youtube.com/playlist?list=PLtw15AaFiBaX85zBtN0n69lzJMnJqqroo)**

## Features

- Fully automatic inflate → hold → controlled-bleed → deflate cycle
- Real-time cuff pressure acquisition via the micro:bit analog input
- Oscillometric detection of systolic and diastolic pressure from the pulse envelope
- Pulse rate extraction from the oscillation peaks
- Live readout on a 128×64 I²C OLED
- Wireless result reporting to a paired micro:bit using the radio module
- Audible/visual feedback for cycle start, completion and error states

## Hardware

| Component | Notes |
|---|---|
| BBC micro:bit (v2 recommended) | Main controller |
| Gauge pressure sensor | MPS20N0040D |
| Instrumentation amplifier | HX710B INA125 — needed to bring the sensor output into the 0–3.3 V ADC range |
| Air pump (DC motor) |  5V DC Air pump |
| Solenoid release valve | For fast deflate / controlled bleed |
| Motor driver | L9110S |
| SSD1306 OLED, 128×64, I²C | Display |
| Arm cuff + tubing + T-connector | Standard sphygmomanometer cuff |


## How it works

1. **Inflate.** The pump runs until cuff pressure exceeds a target above expected systolic (typically ~180 mmHg), then stops.
2. **Bleed.** The valve opens in short pulses to bleed the cuff at roughly 3 mmHg/s while the ADC samples continuously.
3. **Separate the signals.** The raw pressure trace is split into a slowly falling DC baseline (cuff pressure) and a small AC component (arterial pulsations) using a band-pass filter.
4. **Build the envelope.** Peak-to-peak amplitude of each pulsation is measured and plotted against the cuff pressure at which it occurred. The maximum of this envelope corresponds to **mean arterial pressure (MAP)**.
5. **Estimate SYS/DIA.** Fixed-ratio thresholds are applied to the envelope — systolic is read where amplitude rises through ~0.5 × peak on the high-pressure side, diastolic where it falls through ~0.8 × peak on the low-pressure side.
6. **Report.** Values are rendered on the OLED and broadcast over radio, then the valve fully opens to release the cuff.

> Ratio constants and the pressure calibration curve are device-specific.

## Repository structure

### Final project

| File | Description |
|---|---|
| `automatic pump and measurements of blood pressure` | Main application — pump/valve control, ADC sampling, oscillometric algorithm, OLED output |
| `edited communication module code` | Radio transmit/receive layer for sending readings to a paired micro:bit |
| `radio basic feature` | Minimal radio send/receive reference used to develop and test the link |

### Lab 1 — I/O, LEDs and audio

| File | Description |
|---|---|
| `L1 T1 changing light intensity` | Vary LED brightness |
| `L1 T2 change light intensity of 2 color in cyclic order` | Two-colour brightness cycling |
| `L1 T3 change light intensity with combined colour in cyclic order` | Combined-colour brightness cycling |
| `L1 T4 speaker` | Tone generation on the speaker/buzzer |
| `L1 T5 condition like light intensity, smiley face at LED` | Conditional display driven by measured light level |
| `L1 T8 a code that can make the foams oscillate` | Oscillation driver exercise |

### Lab 2 — OLED display and electrical measurement

| File | Description |
|---|---|
| `L2 T1 OLED display` | Initialise and write text to the OLED |
| `L2 T2 display light intensity on OLED` | Stream the light sensor reading to the display |
| `L2 T3 display image on OLED` | Render a bitmap |
| `L2 T4 display an image on the OLED board` | Extended image display exercise |
| `L2 T5 measure the terminal voltage of a source (battery)` | Terminal voltage measurement |
| `L2 T6 measure the voltage, current, and power of one component` | V / I / P measurement |
| `L2 T7 unknown resistor` | Determine an unknown resistance |
| `L2 T8 determine resistor and show it on OLED` | Resistance measurement with OLED readout |


## Calibration

The sensor's raw ADC output must be mapped to mmHg before any of the pressure logic is meaningful.

1. Connect a reference manometer (or a commercial BP monitor) in parallel with the cuff via a T-connector.
2. Record ADC values at several known pressures across the 0–200 mmHg range.
3. Fit a straight line and store the slope and offset in the main program.

```
pressure_mmHg = (adc_reading - OFFSET) * SLOPE
```

`TODO: insert your measured OFFSET and SLOPE, and your validation results against a reference monitor.`

## Limitations

- Single-ratio oscillometric estimation is sensitive to cuff fit, arm movement and bleed rate
- The micro:bit's 10-bit ADC and modest sampling rate limit envelope resolution
- No motion-artifact rejection


## ⚠️ Disclaimer

This is an **educational and prototyping project only**. It is not a medical device, has not been clinically validated, and must not be used to diagnose, monitor or treat any condition. Do not make health decisions based on its readings. Never leave the cuff inflated unattended.

## License

`TODO: add a license, e.g. MIT`

