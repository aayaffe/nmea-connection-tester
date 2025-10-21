# NMEA 0183 Compliance Verification Plan
## RS-422/RS-485 Interface Testing and Validation

**Document Version:** 1.0  
**Date:** 2025-10-21  
**Status:** Draft  
**Author:** Design Team  

---

## 1. Executive Summary

This document outlines the comprehensive testing and verification plan to ensure the RS-422/RS-485 interface design complies with NMEA 0183 electrical and protocol specifications. The plan covers electrical characterization, signal integrity validation, interoperability testing, and environmental qualification.

### Objectives
- Verify compliance with NMEA 0183-HS and NMEA 0183 electrical specifications
- Validate signal integrity at 4800 and 38400 baud rates
- Confirm interoperability with commercial NMEA 0183 devices
- Ensure environmental robustness and ESD protection
- Document test results for certification and quality assurance

---

## 2. Standards and Specifications

### 2.1 Primary Standards

#### NMEA 0183 Standard
- **Version:** 4.11 (latest)
- **Publisher:** National Marine Electronics Association
- **Applicable Sections:**
  - Section 3: Electrical Characteristics
  - Section 4: Data Transmission
  - Appendix A: Connector Specifications

**Key Requirements:**
- Single-ended RS-422 compatible differential signaling
- 4800 baud (standard) and 38400 baud (high-speed) support
- 8 data bits, no parity, 1 stop bit (8N1)
- ASCII character encoding
- Sentence-based data format with checksums

#### RS-422 (EIA-422-A/TIA-422-B)
- **Standard:** TIA/EIA-422-B
- **Description:** Balanced voltage digital interface circuits
- **Key Parameters:**
  - Differential output voltage: ±2V minimum
  - Common-mode voltage range: -7V to +12V
  - Input sensitivity: ±200mV minimum
  - Data rate: Up to 10 Mbps

#### RS-485 (EIA-485-A/TIA-485-A)
- **Standard:** TIA/EIA-485-A (Rev. A)
- **Description:** Electrical characteristics of generators and receivers for use in balanced digital multipoint systems
- **Key Parameters:**
  - Unit load: 1/8 unit load preferred (256 devices on bus)
  - Termination: 120Ω across differential pair

### 2.2 Related Standards

| Standard | Title | Applicability |
|----------|-------|---------------|
| **IEC 61162-1** | Maritime navigation equipment - Digital interfaces - Part 1: Single talker and multiple listeners | International equivalent to NMEA 0183 |
| **ISO 11898** | CAN bus (for comparison) | Reference for differential signaling best practices |
| **IEC 61000-4-2** | ESD immunity testing | ESD protection verification |
| **IEC 61000-4-4** | Electrical fast transient burst | EMI immunity |
| **IPC-2221** | PCB design standards | Layout compliance |

---

## 3. Test Equipment Requirements

### 3.1 Essential Equipment

| Equipment | Specification | Purpose | Example Model |
|-----------|---------------|---------|---------------|
| **Oscilloscope** | ≥200 MHz, 4 channels, ≥1 GSa/s | Signal integrity analysis | Keysight DSOX3024T |
| **Differential Probe** | ≥200 MHz bandwidth | Measure differential signals | Teledyne LeCroy AP034 |
| **Logic Analyzer** | ≥16 channels, 100 MSa/s | Protocol analysis | Saleae Logic Pro 16 |
| **Function Generator** | 2 channels, arbitrary waveform | Signal injection testing | Rigol DG4162 |
| **Digital Multimeter** | 6.5 digit, true RMS | DC measurements | Keithley DMM6500 |
| **Power Supply** | Dual output, 0-30V, 3A | Power system testing | Keysight E36312A |
| **ESD Simulator** | Up to ±15kV, IEC 61000-4-2 | ESD protection validation | Noiseken ESS-2000 |
| **Network Analyzer** | 50Ω, DC-500 MHz (optional) | Impedance measurements | NanoVNA-H4 |

### 3.2 Software Tools

| Tool | Purpose | Source |
|------|---------|--------|
| **OpenCPN** | NMEA 0183 sentence validation | Open source |
| **NMEA Checksum Calculator** | Sentence validation | Online/custom script |
| **Wireshark with NMEA plugin** | Protocol capture and analysis | Open source |
| **Python pySerial** | Automated test scripts | PyPI package |
| **LTspice** | Circuit simulation | Analog Devices (free) |

### 3.3 Reference Devices

| Device Type | Purpose | Examples |
|-------------|---------|----------|
| **NMEA GPS Receiver** | Known-good talker device | Garmin GPS 18x USB |
| **NMEA Chart Plotter** | Known-good listener device | Raymarine Axiom |
| **NMEA Multiplexer** | Multi-device testing | Actisense NDC-4-USB |
| **USB-to-NMEA Adapter** | PC interface reference | Actisense USG-2 |

---

## 4. Test Plan Overview

### 4.1 Test Phases

| Phase | Description | Duration | Prerequisites |
|-------|-------------|----------|---------------|
| **Phase 1** | Design validation simulation | 1 week | Schematic complete |
| **Phase 2** | Prototype electrical testing | 2 weeks | Prototype boards assembled |
| **Phase 3** | Signal integrity validation | 1 week | Phase 2 complete |
| **Phase 4** | Protocol compliance testing | 2 weeks | Phase 3 complete |
| **Phase 5** | Interoperability testing | 2 weeks | Phase 4 complete |
| **Phase 6** | Environmental qualification | 2 weeks | All phases complete |
| **Phase 7** | Documentation and reporting | 1 week | All testing complete |

**Total Estimated Duration:** 11 weeks

---

## 5. Detailed Test Procedures

### 5.1 Phase 1: Design Validation (Simulation)

#### Test 1.1: SPICE Circuit Simulation

**Objective:** Verify circuit operation and component values in simulation

**Procedure:**
1. Create LTspice schematic from design files
2. Model MAX3485 transceiver using SPICE model from manufacturer
3. Include cable model (120Ω twisted pair, capacitance 50 pF/m)
4. Simulate transmit operation:
   - Input: UART signal at 4800 baud
   - Measure differential output voltage (A-B)
   - Verify voltage swing ±2V minimum
5. Simulate receive operation:
   - Input: Differential signal ±2V
   - Measure single-ended output
   - Verify receiver triggers at ±200mV
6. Perform AC analysis for frequency response
7. Transient analysis for rise/fall times

**Pass Criteria:**
- [ ] Differential output voltage ≥ ±2V with 120Ω load
- [ ] Receiver sensitivity ≤ ±200mV
- [ ] Rise/fall time < 30% of bit period at 38400 baud
- [ ] No oscillations or ringing

**Documentation:**
- Simulation schematics
- Waveform plots (time domain)
- Frequency response plots
- Component stress analysis

---

#### Test 1.2: Signal Integrity Simulation

**Objective:** Validate signal quality with transmission line effects

**Tool:** HyperLynx, ADS, or similar SI tool

**Procedure:**
1. Import PCB layout (Gerber or ODB++)
2. Define stackup and trace parameters
3. Extract differential impedance of A/B traces
4. Run eye diagram simulation at 38400 baud
5. Check for:
   - Eye opening (voltage margin)
   - Jitter (timing margin)
   - Crosstalk from adjacent traces
   - Reflections from impedance mismatch

**Pass Criteria:**
- [ ] Eye opening > 75% of signal amplitude
- [ ] Peak-to-peak jitter < 10% of bit period
- [ ] Differential impedance 100Ω ±10%
- [ ] Return loss < -10 dB up to 1 MHz

**Documentation:**
- Eye diagram plots
- S-parameter measurements
- Impedance vs. frequency plots
- Crosstalk analysis report

---

### 5.2 Phase 2: Prototype Electrical Testing

#### Test 2.1: Power Supply Verification

**Objective:** Verify power distribution and decoupling

**Equipment:** DMM, Oscilloscope

**Procedure:**
1. Connect 3.3V power supply to VCC input
2. Measure voltage at MAX3485 VCC pin
3. Verify voltage within specification (3.0V to 3.6V)
4. Measure current consumption:
   - Idle state (DE/RE = 0)
   - Transmit state (DE/RE = 1)
5. Probe VCC pin with oscilloscope (AC coupled, 50 mV/div)
6. Check for power supply noise during transmit/receive
7. Verify decoupling capacitor placement with thermal imaging

**Pass Criteria:**
- [ ] VCC within ±5% of nominal (3.3V)
- [ ] Idle current < 500 µA
- [ ] Transmit current < 10 mA
- [ ] VCC ripple < 50 mV peak-to-peak
- [ ] No thermal hotspots on decoupling capacitors

**Documentation:**
- Voltage measurements table
- Current consumption data
- Power supply noise waveforms
- Thermal images

---

#### Test 2.2: DC Electrical Characteristics

**Objective:** Verify DC parameters of RS-485 interface

**Equipment:** DMM, Current Source

**Test 2.2.1: Output Voltage (Driver)**

**Procedure:**
1. Set DE/RE = 1 (transmit mode)
2. Apply logic high (3.3V) to DI pin
3. Measure voltage at A and B pins (no load)
4. Calculate differential voltage VOD = VA - VB
5. Repeat with logic low (0V) on DI pin
6. Measure with 120Ω termination resistor
7. Measure with 60Ω load (worst case)

**Pass Criteria:**
- [ ] |VOD| ≥ 1.5V with 54Ω differential load (RS-485 spec)
- [ ] |VOD| ≥ 2.0V with 120Ω termination
- [ ] Common-mode voltage within -7V to +12V

**Test 2.2.2: Input Voltage (Receiver)**

**Procedure:**
1. Set DE/RE = 0 (receive mode)
2. Apply differential voltage to A and B pins
3. Start with VID = 0V (differential input)
4. Gradually increase VID until RO output toggles
5. Record threshold voltage
6. Repeat for negative direction
7. Test with common-mode voltage from -7V to +12V

**Pass Criteria:**
- [ ] Receiver threshold: |VID| ≤ 200 mV (RS-485 spec)
- [ ] Hysteresis present (typically 50-70 mV)
- [ ] Receiver functions across full common-mode range

**Test 2.2.3: Failsafe Operation**

**Procedure:**
1. Disconnect A and B inputs (open circuit)
2. Verify RO output is high (logic 1) due to bias resistors
3. Short A and B inputs together
4. Verify RO output is high (logic 1) due to bias resistors

**Pass Criteria:**
- [ ] RO = high when inputs open
- [ ] RO = high when inputs shorted together
- [ ] Failsafe ensured by R3/R4 bias network

**Documentation:**
- VOD measurements vs. load resistance
- Receiver threshold measurements
- Common-mode range test results
- Failsafe test results

---

#### Test 2.3: ESD Protection Validation

**Objective:** Verify ESD protection diodes function correctly

**Equipment:** ESD Simulator, Oscilloscope

**Procedure:**
1. Configure board with ESD ground strap
2. Apply ESD pulse to A line:
   - Contact discharge: ±8kV, ±12kV, ±15kV
   - Air discharge: ±15kV
3. Monitor A line voltage with oscilloscope
4. Verify clamping voltage < 10V
5. Repeat for B line
6. After each test, verify circuit still functions
7. Perform 10 pulses at each level per IEC 61000-4-2

**Pass Criteria:**
- [ ] Survives ±12kV contact discharge (minimum)
- [ ] Clamping voltage < 10V
- [ ] No permanent damage to circuit
- [ ] Circuit remains functional after test
- [ ] Optional: ±15kV for enhanced robustness

**Documentation:**
- ESD test setup photos
- Clamping voltage waveforms
- Before/after functional test results
- Test report per IEC 61000-4-2 format

---

### 5.3 Phase 3: Signal Integrity Validation

#### Test 3.1: Eye Diagram Measurement

**Objective:** Validate signal quality at both baud rates

**Equipment:** Oscilloscope with eye diagram capability, BERT (optional)

**Procedure for 4800 baud:**
1. Configure MCU to transmit continuous UART pattern
2. Use pattern: 0xAA (alternating bits) or PRBS7
3. Probe differential signal (A-B) with differential probe
4. Set oscilloscope to infinite persistence
5. Trigger on rising edge
6. Capture for 10,000 bit periods
7. Measure eye opening (vertical and horizontal)
8. Measure jitter (peak-to-peak and RMS)

**Procedure for 38400 baud:**
1. Repeat above steps at 38400 baud
2. Pay attention to rise/fall times and overshoot
3. Check for ISI (inter-symbol interference)

**Pass Criteria:**
- [ ] Eye opening (voltage): > 1.5V at receiver input
- [ ] Eye opening (time): > 75% of bit period
- [ ] Peak-to-peak jitter < 10% of bit period
- [ ] RMS jitter < 5% of bit period
- [ ] No overshoot > 20% of signal amplitude
- [ ] Clean eye with minimal ISI

**Documentation:**
- Eye diagram screenshots at both baud rates
- Jitter histogram plots
- Measurement summary table
- Comparison to simulation results

---

#### Test 3.2: Cable Length Testing

**Objective:** Determine maximum cable length for reliable operation

**Equipment:** Oscilloscope, Variable-length cable or cable simulator

**Procedure:**
1. Start with short cable (1 meter)
2. Transmit continuous NMEA sentence at 4800 baud
3. Verify error-free reception
4. Increase cable length in 10-meter increments
5. Monitor signal quality with oscilloscope at each length
6. Record maximum length where:
   - Eye is still open
   - BER < 10^-9 (no errors in 1 billion bits)
7. Repeat test at 38400 baud

**Pass Criteria:**
- [ ] 4800 baud: ≥ 1000 meters (NMEA spec)
- [ ] 38400 baud: ≥ 300 meters
- [ ] Signal amplitude at max length > 300 mV differential

**Documentation:**
- Cable length vs. signal amplitude plot
- Cable length vs. BER plot
- Eye diagrams at various cable lengths
- Maximum length determination report

---

#### Test 3.3: Termination Resistor Validation

**Objective:** Verify importance and value of termination resistor

**Equipment:** Oscilloscope, TDR (optional)

**Procedure:**
1. **Test A: No Termination**
   - Remove RT1 (120Ω termination)
   - Transmit data at 38400 baud with 10m cable
   - Observe signal with oscilloscope
   - Look for reflections and ringing
2. **Test B: With Termination**
   - Install RT1 (120Ω)
   - Repeat transmission
   - Compare waveform quality
3. **Test C: Incorrect Termination**
   - Try 75Ω, 150Ω, 220Ω values
   - Observe effects on signal quality
4. **Optional: TDR Measurement**
   - Use TDR to measure cable impedance
   - Verify ~120Ω characteristic impedance

**Pass Criteria:**
- [ ] Significant ringing/reflection without termination
- [ ] Clean signal with 120Ω termination
- [ ] 120Ω provides best performance vs. other values

**Documentation:**
- Waveform comparisons (terminated vs. unterminated)
- Photos of different termination scenarios
- TDR measurement report (if available)

---

### 5.4 Phase 4: Protocol Compliance Testing

#### Test 4.1: UART Parameter Verification

**Objective:** Confirm UART settings match NMEA 0183 specification

**Equipment:** Logic analyzer, UART decoder

**Procedure:**
1. Connect logic analyzer to DI and RO pins
2. Transmit known NMEA sentence (e.g., $GPGGA)
3. Capture and decode UART transmission
4. Verify parameters:
   - Baud rate: 4800 or 38400 bps
   - Data bits: 8
   - Parity: None
   - Stop bits: 1
   - Bit order: LSB first
5. Measure actual baud rate accuracy
6. Check start bit duration
7. Check stop bit duration

**Pass Criteria:**
- [ ] Baud rate within ±1% of nominal
- [ ] All UART parameters match 8N1 configuration
- [ ] Start and stop bits properly formed
- [ ] No framing errors
- [ ] No parity errors (should be N/A)

**Documentation:**
- Logic analyzer captures with annotations
- UART parameter measurement table
- Baud rate accuracy calculation
- Sample NMEA sentences decoded

---

#### Test 4.2: NMEA Sentence Format Validation

**Objective:** Verify proper NMEA 0183 sentence structure

**Equipment:** Logic analyzer or serial terminal, NMEA sentence validator

**Test Sentences:**
```
$GPGGA,123519,4807.038,N,01131.000,E,1,08,0.9,545.4,M,46.9,M,,*47
$GPGSA,A,3,04,05,,09,12,,,24,,,,,2.5,1.3,2.1*39
$GPRMC,123519,A,4807.038,N,01131.000,E,022.4,084.4,230394,003.1,W*6A
```

**Procedure:**
1. Transmit test sentences from MCU
2. Capture with logic analyzer or serial terminal
3. Validate each sentence:
   - Starts with '$' (0x24)
   - Ends with CR+LF (0x0D 0x0A)
   - Contains valid checksum after '*'
   - Checksum calculated as XOR of all chars between '$' and '*'
   - Maximum length ≤ 82 characters
   - Only ASCII printable characters
4. Use NMEA validator tool to check format
5. Test with intentional errors (bad checksum, invalid chars)

**Pass Criteria:**
- [ ] All sentences start with '$'
- [ ] All sentences end with CR+LF
- [ ] Checksums calculated correctly
- [ ] Sentence length ≤ 82 characters
- [ ] Only valid NMEA sentence IDs used
- [ ] Field delimiters (,) properly placed

**Documentation:**
- Sample sentence captures
- Checksum verification calculations
- Format validation report
- Error handling test results

---

#### Test 4.3: Timing and Sentence Spacing

**Objective:** Verify timing between sentences meets NMEA requirements

**Equipment:** Logic analyzer with timing measurements

**Procedure:**
1. Transmit multiple NMEA sentences consecutively
2. Measure time between end of one sentence (LF) and start of next ('$')
3. Verify minimum inter-sentence gap
4. Test maximum sentence rate
5. Verify sentences don't overlap or collide

**Pass Criteria:**
- [ ] Inter-sentence gap ≥ 10 ms (typical)
- [ ] Maximum rate ≤ 1 sentence per 10 ms at 4800 baud
- [ ] No sentence truncation or collision

**Documentation:**
- Timing diagram with measurements
- Inter-sentence gap statistics
- Maximum sustainable data rate

---

### 5.5 Phase 5: Interoperability Testing

#### Test 5.1: Talker Mode - Connect to Commercial Listener

**Objective:** Verify device can transmit to commercial NMEA receivers

**Equipment:** 
- Commercial NMEA GPS receiver or chart plotter
- NMEA sentence source (GPS simulator or pre-recorded data)

**Procedure:**
1. Configure device under test (DUT) as talker (transmit mode)
2. Connect to commercial NMEA listener (e.g., Garmin GPS display)
3. Transmit standard NMEA sentences:
   - $GPGGA (GPS fix data)
   - $GPRMC (Recommended minimum)
   - $GPVTG (Course and speed)
4. Verify listener device correctly receives and displays data
5. Test at both 4800 and 38400 baud (if listener supports)
6. Test with cable lengths: 1m, 10m, 50m, 100m
7. Document any compatibility issues

**Pass Criteria:**
- [ ] Listener receives all transmitted sentences
- [ ] No data corruption or CRC errors
- [ ] Position, time, course data displayed correctly
- [ ] Works at standard cable lengths (≥50m)
- [ ] Compatible with ≥3 different listener devices

**Test Matrix:**

| Listener Device | Baud Rate | Cable Length | Result | Notes |
|-----------------|-----------|--------------|--------|-------|
| Garmin GPS 18x | 4800 | 10m | PASS/FAIL | |
| Garmin GPS 18x | 4800 | 50m | PASS/FAIL | |
| Raymarine Axiom | 4800 | 10m | PASS/FAIL | |
| Lowrance HDS | 38400 | 10m | PASS/FAIL | |

**Documentation:**
- Test matrix with results
- Photos of connected systems
- Screenshots of received data on listener
- Any compatibility notes or issues

---

#### Test 5.2: Listener Mode - Receive from Commercial Talker

**Objective:** Verify device can receive from commercial NMEA transmitters

**Equipment:**
- Commercial NMEA GPS receiver (talker)
- Serial terminal or logic analyzer for verification

**Procedure:**
1. Configure DUT as listener (receive mode)
2. Connect to commercial NMEA talker (e.g., Garmin GPS)
3. Verify DUT receives sentences correctly
4. Capture received data with serial terminal
5. Validate sentence format and checksums
6. Test at both 4800 and 38400 baud
7. Test with cable lengths: 1m, 10m, 50m, 100m
8. Test with multiple talkers simultaneously (multi-drop)

**Pass Criteria:**
- [ ] Receives all transmitted sentences without errors
- [ ] Checksums validate correctly
- [ ] No missed or corrupted sentences
- [ ] Works with ≥3 different talker devices
- [ ] Multi-drop operation successful (if applicable)

**Test Matrix:**

| Talker Device | Baud Rate | Cable Length | Error Rate | Result |
|---------------|-----------|--------------|------------|--------|
| Garmin GPS 18x | 4800 | 10m | 0% | PASS/FAIL |
| Garmin GPS 18x | 4800 | 50m | 0% | PASS/FAIL |
| Furuno GP170 | 4800 | 10m | 0% | PASS/FAIL |
| Actisense NGW-1 | 38400 | 10m | 0% | PASS/FAIL |

**Documentation:**
- Test matrix with error rates
- Sample received sentences
- Multi-drop configuration diagram
- Compatibility report

---

#### Test 5.3: Half-Duplex Operation

**Objective:** Verify proper transmit/receive switching

**Equipment:** 2x DUT boards, Oscilloscope

**Procedure:**
1. Configure DUT #1 as talker, DUT #2 as listener
2. Connect both to same RS-485 bus
3. Transmit sentence from DUT #1
4. Verify DUT #2 receives correctly
5. Monitor DE/RE control signal with oscilloscope
6. Verify timing:
   - DE asserted before data transmission
   - DE held during transmission
   - DE deasserted after transmission complete
   - Proper turnaround time between TX and RX
7. Switch roles and repeat
8. Test rapid TX/RX switching

**Pass Criteria:**
- [ ] DE/RE timing correct (setup/hold times met)
- [ ] No bus contention (both transmitting simultaneously)
- [ ] Proper turnaround time (typically 1-10 µs)
- [ ] No data corruption during mode switching
- [ ] LED indicators correctly show TX/RX activity

**Documentation:**
- Timing diagram of DE/RE control with data
- Oscilloscope captures showing transitions
- Test sequence log
- Bus contention test results (should be none)

---

### 5.6 Phase 6: Environmental Qualification

#### Test 6.1: Temperature Cycling

**Objective:** Verify operation across full temperature range

**Equipment:** Environmental chamber, Data logger

**Procedure:**
1. Place DUT in environmental chamber
2. Connect to test equipment outside chamber (long cables)
3. Temperature profile:
   - Start: 25°C (1 hour soak)
   - Ramp to -40°C over 1 hour
   - Hold at -40°C for 4 hours (continuous operation test)
   - Ramp to +85°C over 1 hour
   - Hold at +85°C for 4 hours (continuous operation test)
   - Return to 25°C
   - Repeat cycle 5 times
4. During each temperature hold:
   - Transmit continuous NMEA sentences
   - Monitor for errors
   - Log BER (bit error rate)
   - Check LED operation
5. Perform full functional test at each extreme
6. Inspect for physical damage after cycling

**Pass Criteria:**
- [ ] Operates correctly from -40°C to +85°C
- [ ] BER < 10^-9 at all temperatures
- [ ] No permanent degradation after cycling
- [ ] All components within specifications
- [ ] No cracks, solder joint failures, or physical damage

**Documentation:**
- Temperature profile chart
- BER vs. temperature plot
- Visual inspection report with photos
- Functional test results at temperature extremes

---

#### Test 6.2: Humidity Testing

**Objective:** Verify operation in humid environments (marine application)

**Equipment:** Humidity chamber

**Procedure:**
1. Place DUT in humidity chamber
2. Set conditions: 85°C / 85% RH (IPC-TM-650 method)
3. Duration: 168 hours (7 days) continuous
4. Perform functional test every 24 hours
5. Monitor for:
   - Condensation on board
   - Corrosion on exposed metal
   - Insulation resistance degradation
6. Final test after removal and 24-hour recovery

**Pass Criteria:**
- [ ] Operates correctly throughout test
- [ ] No visible corrosion
- [ ] Insulation resistance > 10 MΩ
- [ ] No degradation in signal quality

**Documentation:**
- Humidity test log
- Photos before/after/during test
- Insulation resistance measurements
- Functional test results

---

#### Test 6.3: Vibration Testing

**Objective:** Verify mechanical robustness (handheld device requirement)

**Equipment:** Vibration table, Accelerometer

**Test Profile:** IEC 60068-2-64 (Random Vibration)

**Procedure:**
1. Mount DUT to vibration table
2. Attach accelerometer to board
3. Apply random vibration profile:
   - Frequency range: 10-500 Hz
   - Amplitude: 2 grms
   - Duration: 1 hour per axis (X, Y, Z)
   - Total duration: 3 hours
4. Monitor operation during vibration:
   - Connect to test system with flex cables
   - Continuous NMEA transmission
   - Monitor for intermittent failures
5. Visual inspection after test
6. Full functional test after test

**Pass Criteria:**
- [ ] No intermittent failures during vibration
- [ ] No loose components
- [ ] No solder joint cracks
- [ ] All functions operational after test
- [ ] Connector remains secure

**Documentation:**
- Vibration profile data
- Accelerometer readings
- Before/after photos
- Functional test report

---

#### Test 6.4: EMI/EMC Testing

**Objective:** Verify electromagnetic compatibility

**Standards:**
- **Radiated Emissions:** FCC Part 15 Class B (or CISPR 22 Class B)
- **Conducted Emissions:** FCC Part 15 / CISPR 22
- **Radiated Immunity:** IEC 61000-4-3 (10 V/m)
- **EFT/Burst:** IEC 61000-4-4 (±2 kV)

**Note:** Full EMC testing typically requires certified test lab.

**Pre-Compliance Testing (In-House):**

**Equipment:** Near-field probe set, Spectrum analyzer

**Procedure:**
1. Set up device in anechoic chamber or large room
2. Device operating: continuous NMEA transmission at 38400 baud
3. Scan with near-field probe around:
   - Transceiver IC
   - Differential traces
   - Connector
   - LEDs and associated traces
4. Measure radiated emissions with spectrum analyzer
5. Compare to FCC Part 15 Class B limits
6. Identify potential problems for mitigation
7. Test common-mode choke (if implemented) for improvement

**Pass Criteria (Preliminary):**
- [ ] Radiated emissions below Class B limits (with margin)
- [ ] No unexpected spurious emissions
- [ ] Device operates in presence of simulated interference
- [ ] Common-mode choke reduces emissions (if used)

**Note:** Final certification requires accredited test lab.

**Documentation:**
- Near-field scan results
- Spectrum analyzer screenshots
- Emissions vs. limits plot
- Problem areas identified and mitigated

---

### 5.7 Phase 7: Reliability Testing

#### Test 7.1: Accelerated Life Testing

**Objective:** Predict long-term reliability

**Procedure:**
1. Select sample size: 10 units minimum
2. Operate at elevated temperature (70°C ambient)
3. Continuous operation: 1000 hours
4. Equivalent lifetime: ~10,000 hours at 25°C (using Arrhenius equation)
5. Perform functional test every 100 hours
6. Monitor for:
   - Degradation in signal quality
   - Increased error rate
   - Component failures
7. Weibull analysis if failures occur

**Pass Criteria:**
- [ ] Zero failures during test (target)
- [ ] No degradation in performance
- [ ] MTBF > 100,000 hours (predicted)

**Documentation:**
- Test setup and conditions
- Hourly log of operation
- Failure analysis (if any)
- Reliability prediction report

---

#### Test 7.2: Connector Durability

**Objective:** Verify connector mating cycle life

**Procedure:**
1. For RJ45 connector:
   - Mate and demate 1000 times
   - Check for wear, damage
   - Measure contact resistance periodically
2. For screw terminal:
   - Tighten and loosen 100 times
   - Check for thread wear
   - Verify secure connection

**Pass Criteria:**
- [ ] No visible damage after cycling
- [ ] Contact resistance remains < 0.1Ω
- [ ] Secure connection maintained

---

## 6. Test Documentation Requirements

### 6.1 Test Report Template

Each test shall document:

**1. Test Information**
- Test ID and title
- Date and time
- Test engineer name
- DUT serial number
- Software/firmware version

**2. Test Setup**
- Equipment list with calibration dates
- Setup diagram or photo
- Environmental conditions

**3. Procedure**
- Step-by-step procedure followed
- Any deviations from plan

**4. Results**
- Measured data (tables, graphs)
- Pass/fail determination
- Observations and notes

**5. Conclusion**
- Summary of results
- Issues found and resolution
- Recommendations

### 6.2 Acceptance Criteria Summary

For device to pass compliance verification:

| Category | Requirement | Status |
|----------|-------------|--------|
| **Electrical** | Meets RS-422/485 specs | ☐ |
| **Signal Integrity** | Eye diagram open at 38400 baud | ☐ |
| **Protocol** | NMEA 0183 format validated | ☐ |
| **Interoperability** | Works with ≥3 commercial devices | ☐ |
| **Temperature** | -40°C to +85°C operation | ☐ |
| **ESD** | Survives ±12kV contact discharge | ☐ |
| **EMC** | Pre-compliance passed | ☐ |
| **Reliability** | 1000-hour HALT with zero failures | ☐ |

**Overall Status:** ☐ PASS ☐ FAIL ☐ CONDITIONAL PASS

---

## 7. Compliance Certification

### 7.1 NMEA Certification (Optional)

**Process:**
1. Apply for NMEA manufacturer membership
2. Submit design documentation
3. Provide sample devices for testing
4. NMEA lab performs compliance testing
5. Certification issued if passed

**Cost:** ~$5,000-10,000 USD  
**Duration:** 2-3 months  
**Benefits:**
- Use NMEA logo
- Listed on NMEA website
- Market credibility

**Note:** Not required for legal sale, but provides market differentiation.

### 7.2 FCC Certification (if applicable)

**Required if:** Device is sold commercially in USA

**Process:**
1. Pre-compliance testing (in-house)
2. Submit to FCC-certified test lab
3. EMI/EMC testing per FCC Part 15
4. Lab issues test report
5. Submit FCC Form 731 and test report
6. Receive FCC ID

**Cost:** ~$2,000-5,000 USD  
**Duration:** 2-4 weeks

### 7.3 CE Marking (if applicable)

**Required if:** Device is sold in European Union

**Process:**
1. Determine applicable directives:
   - EMC Directive 2014/30/EU
   - RoHS Directive 2011/65/EU
   - Marine Equipment Directive (MED) if applicable
2. Perform EMC testing per EN standards
3. Prepare technical file
4. Issue Declaration of Conformity
5. Affix CE marking

**Cost:** ~€3,000-7,000 EUR  
**Duration:** 4-6 weeks

---

## 8. Risk Assessment

### 8.1 Potential Failure Modes

| Failure Mode | Probability | Impact | Mitigation |
|--------------|-------------|--------|------------|
| ESD damage | Medium | High | TVS diodes, robust layout |
| Cable impedance mismatch | Low | Medium | Specify 120Ω cable, provide termination |
| EMI susceptibility | Medium | Medium | Shielding, filtering, proper layout |
| Component obsolescence | Low | High | Multi-source components |
| Connector wear | Medium | Low | Quality connectors, strain relief |
| Software UART errors | Low | Medium | Thorough firmware testing |

### 8.2 Mitigation Strategies

1. **Design Robustness:**
   - Use proven transceiver (MAX3485)
   - Multiple layers of ESD protection
   - Conservative design margins

2. **Component Selection:**
   - Multi-sourced components
   - Long lifecycle parts
   - Quality brands (Analog Devices, TI, NXP)

3. **Manufacturing:**
   - Automated assembly reduces defects
   - 100% functional testing
   - Quality control procedures

4. **Documentation:**
   - Comprehensive user manual
   - Installation guidelines
   - Troubleshooting guide

---

## 9. Schedule and Resources

### 9.1 Testing Schedule

| Week | Phase | Activities | Deliverables |
|------|-------|------------|--------------|
| 1 | Phase 1 | SPICE simulation, SI analysis | Simulation reports |
| 2-3 | Phase 2 | Electrical testing | Electrical test report |
| 4 | Phase 3 | Signal integrity | Eye diagrams, timing analysis |
| 5-6 | Phase 4 | Protocol testing | Protocol compliance report |
| 7-8 | Phase 5 | Interoperability | Interop test matrix |
| 9-10 | Phase 6 | Environmental | Temp, humidity, vibration reports |
| 11 | Phase 7 | Documentation | Final compliance report |

### 9.2 Resource Requirements

**Personnel:**
- Test engineer: 11 weeks full-time
- Hardware engineer: 2 weeks (support)
- Firmware engineer: 2 weeks (test fixtures)

**Equipment:**
- Existing lab equipment: Oscilloscope, DMM, etc.
- Rental: ESD simulator (~$500/week)
- External lab: EMC testing (~$3,000)

**Samples:**
- 20 prototype boards
- 5 boards for destructive testing

**Budget Estimate:** $15,000-20,000 USD

---

## 10. Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-10-21 | Design Team | Initial compliance verification plan |

---

## 11. Appendices

### Appendix A: References

1. **NMEA 0183 Standard Version 4.11**  
   National Marine Electronics Association

2. **TIA/EIA-422-B**  
   Electrical Characteristics of Balanced Voltage Digital Interface Circuits

3. **TIA/EIA-485-A Rev. A**  
   Electrical Characteristics of Generators and Receivers for Use in Balanced Digital Multipoint Systems

4. **IEC 61162-1**  
   Maritime navigation and radiocommunication equipment - Digital interfaces

5. **AN-960: RS-485/RS-422 Circuit Implementation Guide**  
   Analog Devices Application Note

6. **SLLA414: RS-485 PCB Layout Guidelines**  
   Texas Instruments Application Report

### Appendix B: NMEA 0183 Sentence Examples

Common sentences for testing:
```
$GPGGA,123519,4807.038,N,01131.000,E,1,08,0.9,545.4,M,46.9,M,,*47
$GPGLL,4916.45,N,12311.12,W,225444,A,*1D
$GPGSA,A,3,04,05,,09,12,,,24,,,,,2.5,1.3,2.1*39
$GPGSV,2,1,08,01,40,083,46,02,17,308,41,12,07,344,39,14,22,228,45*75
$GPRMC,123519,A,4807.038,N,01131.000,E,022.4,084.4,230394,003.1,W*6A
$GPVTG,054.7,T,034.4,M,005.5,N,010.2,K*48
```

### Appendix C: Test Checklist

**Pre-Test Setup:**
- [ ] Equipment calibrated (within last 12 months)
- [ ] DUT serial number recorded
- [ ] Test fixtures prepared
- [ ] Safety review completed
- [ ] Test plan reviewed with team

**During Testing:**
- [ ] Photos of test setup taken
- [ ] All data logged in real-time
- [ ] Anomalies documented immediately
- [ ] Equipment settings recorded

**Post-Test:**
- [ ] Data backed up
- [ ] Test report completed within 48 hours
- [ ] Failures analyzed and documented
- [ ] Next steps identified
- [ ] Test equipment returned/stored properly

---

**End of Compliance Verification Plan**
