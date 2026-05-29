# Variable Speed Centrifuge

`> Hardware build in progress — documentation added ahead of completion.`

Built for a Karachi-based engineering startup as a low-cost alternative to commercial centrifuge instruments. Commercial units from companies like OFITE typically cost $500–$1,500 USD — this builds a functional equivalent for under $90.

## What is this device and what does it do

A centrifuge is an instrument that spins liquid samples at high speed to separate their components by density. A sample tube is placed into the rotor, which spins at up to 2,000 RPM. The centrifugal force pushes denser particles — solids and heavy liquids — outward to the bottom of the tube, while lighter components like oil and water rise to the top. This separates mixtures such as oil, water, and solid phases in drilling fluid or mud samples for measurement and analysis.

This build replicates the OFITE model #153-25-15 portable centrifuge but improves on it significantly. The original OFITE unit runs at a fixed 1,750 RPM with no speed control and no RPM display. This version features a fully automatic ramp-up sequence from 0 to 2,000 RPM in controlled steps, live RPM display, and safety features including an emergency stop and lid interlock.

## Why is it needed

Industries like oil drilling and wastewater treatment need to analyze drilling fluid and mud samples by separating their oil, water, and solid components. Without centrifuge analysis, operators cannot accurately measure fluid composition. Commercial centrifuge units from companies like OFITE cost hundreds to over a thousand dollars. This project builds a fully functional equivalent for under $90 using an RS755 DC motor, an Arduino Nano, and basic electronic components.

## Components used

- RS755 Brushed DC Motor (12V)
- BTS7960 43A PWM Motor Driver Module
- Arduino Nano (ATmega328P)
- TM1637 4-digit 7-segment LED display
- A3144 Hall Effect Sensor
- N42 Neodymium Disc Magnet (5mm x 2mm) for RPM detection
- LM2596 DC-DC Buck Converter Module (12V to 5V)
- 12V 5A Switching Power Supply
- Illuminated momentary pushbutton (Start, green, 22mm panel mount)
- Red mushroom head latching pushbutton (Emergency Stop, 22mm panel mount)
- 5A slow-blow inline fuse with panel mount holder
- 100µF 25V electrolytic capacitor x2 (motor noise suppression)
- 0.1µF 50V ceramic capacitor x2 (motor noise suppression)
- 10µF 16V electrolytic capacitor x2 (Arduino decoupling)
- 10kΩ resistor (Hall sensor pull-up)
- 220Ω resistor (button LED current limiting)
- 18 AWG multi-color wire (motor power circuit)
- 22 AWG multi-color wire (logic and signal circuit)
- Perfboard for permanent circuit assembly
- Metal enclosure with hinged lid
- 8mm rigid shaft coupler (magnet mount)
- Centrifuge rotor plate head (2-place, 12.5 mL tubes)

## How it works

The Arduino Nano controls the entire system. When the Start button is pressed, the Arduino checks that the lid is closed via the lid interlock switch, then enables the BTS7960 motor driver and sends a PWM signal on pin D9 to begin spinning the RS755 motor. The motor ramps up automatically in four steps — 500, 1,000, 1,500, and 2,000 RPM — spending exactly 5 seconds at each step before increasing. At 20 seconds the motor holds at 2,000 RPM until manually stopped.

RPM is measured using an A3144 Hall effect sensor mounted on a fixed bracket inside the enclosure, positioned 2–4mm from a neodymium magnet epoxied to the side of the shaft coupler. Every full revolution of the motor shaft brings the magnet past the sensor face, generating a falling edge pulse on Arduino pin D5 via a hardware interrupt. The Arduino counts these pulses over a 500ms window and calculates RPM using the formula RPM = pulses x 120. The result is displayed live on the TM1637 4-digit display and updated every 500ms. A 3-reading rolling average is applied in software to smooth out any interference-related glitches.

Motor noise from brush sparking is suppressed by 100µF and 0.1µF capacitors soldered directly at the motor terminals, a 10kΩ pull-up resistor and 0.1µF capacitor on the Hall sensor signal line, and sensor wiring routed at least 30mm away from the motor body.

The Emergency Stop button on pin D3 cuts motor power instantly at any point in the ramp sequence. The lid interlock microswitch on pin D4 prevents the motor from running if the enclosure lid is open, and cuts power immediately if the lid is opened during operation.

## Challenges I ran into

The biggest challenge was motor interference affecting the Hall sensor readings. The RS755 is a brushed DC motor whose carbon brushes generate electromagnetic interference through both conducted noise on the power wires and radiated EMI through the air. This caused erratic RPM readings during early testing. The fix was adding suppression capacitors directly at the motor terminals, adding a pull-up resistor and decoupling capacitor on the sensor signal line, and routing the sensor wire well away from the motor body.
