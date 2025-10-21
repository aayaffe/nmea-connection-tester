# RS-422/RS-485 Interface Schematic for NMEA 0183

## Overview
This schematic describes the differential signaling interface circuit for NMEA 0183 compatibility using RS-422/RS-485 transceivers.

## Circuit Design

### Main Components

#### 1. Differential Transceiver (U1)
**Selected Component: MAX3485ESA+**
- **Manufacturer:** Maxim Integrated (Analog Devices)
- **Package:** SOIC-8
- **Operating Voltage:** 3.3V (compatible with modern microcontrollers)
- **Data Rate:** Up to 10 Mbps (supports required 4800 and 38400 baud)
- **Features:**
  - Half-duplex RS-485/RS-422 transceiver
  - Low power consumption (375µA operating, 0.1µA shutdown)
  - Extended common-mode range: -7V to +12V
  - Fail-safe receiver input
  - Slew-rate limited for reduced EMI
  - ESD protection: ±15kV HBM on RS-485 pins

**Pinout:**
1. RO (Receiver Output) → MCU RX
2. RE (Receiver Enable, active low) → MCU GPIO (tied to DE)
3. DE (Driver Enable, active high) → MCU GPIO (tied to RE)
4. DI (Driver Input) → MCU TX
5. GND (Ground)
6. A (Non-inverting Driver Output/Receiver Input)
7. B (Inverting Driver Output/Receiver Input)
8. VCC (+3.3V)

**Alternative Components:**
- MAX485ESA+ (lower cost, 5V operation)
- SN65HVD78DR (TI, automotive grade, better ESD)
- LTC2850IS8 (Linear Tech, excellent EMI performance)

### 2. ESD Protection (D1, D2)

**Component: PRTR5V0U2X (NXP)**
- **Type:** Ultra-low capacitance TVS diode array
- **Configuration:** 2 channels for A and B lines
- **Protection Level:** ±12kV contact discharge (IEC 61000-4-2)
- **Clamping Voltage:** 9.7V @ 1A
- **Capacitance:** 0.8pF typical (preserves signal integrity at 38400 baud)
- **Package:** SOT-143

**Connection:**
- Pin 1: GND
- Pin 2: I/O A (to transceiver pin 6)
- Pin 3: I/O B (to transceiver pin 7)
- Pin 4: VCC (optional, leave NC for unidirectional)

**Alternative:** PESD3V3L2BT (lower cost option)

### 3. Termination Resistor Network

**RT1: 120Ω ±1% 1/4W resistor**
- Placed between A and B lines
- Matches twisted pair cable impedance
- Required for proper signal termination

**Switchable Termination (Optional):**
- **SW1:** SPST DIP switch or jumper
- Allows enabling/disabling termination
- Required when device is at end of bus vs. middle

**Components:**
- RT1: 120Ω, 1%, 1/4W, 0805 package
- SW1: CTS 206-2MST SPST DIP switch
- Alternative: 2-pin header with jumper cap

### 4. Status LEDs

#### TX Activity LED (LED1)
**Component: Green 0805 LED**
- Forward Voltage: 2.0V typical
- Forward Current: 20mA
- Resistor R1: 150Ω (for 3.3V supply)
- Connection: MCU GPIO → R1 → LED1 → GND

**Circuit:**
```
MCU_TX_LED (GPIO) ──[R1: 150Ω]──┤>├── GND
                                LED1
```

#### RX Activity LED (LED2)
**Component: Yellow/Amber 0805 LED**
- Forward Voltage: 2.1V typical
- Forward Current: 20mA
- Resistor R2: 150Ω (for 3.3V supply)
- Connection: MCU GPIO → R2 → LED2 → GND

**Circuit:**
```
MCU_RX_LED (GPIO) ──[R2: 150Ω]──┤>├── GND
                                LED2
```

### 5. Bias and Failsafe Network

**For RS-485 operation (ensuring defined idle state):**

**R3: 560Ω pullup resistor** (A line to VCC)
**R4: 560Ω pulldown resistor** (B line to GND)

These resistors ensure a defined differential voltage when no driver is active, providing receiver failsafe operation.

**Circuit:**
```
VCC (3.3V)
    |
   [R3: 560Ω]
    |
    +──── A line ────[RT1: 120Ω]──── B line
                                          |
                                      [R4: 560Ω]
                                          |
                                         GND
```

### 6. Power Supply Decoupling

**C1: 100nF ceramic capacitor (X7R, 0805)**
- Placed close to VCC pin of MAX3485
- Low ESR for high-frequency noise filtering

**C2: 10µF ceramic capacitor (X7R, 0805)**
- Bulk capacitance for transient currents
- Placed near power input

**Circuit:**
```
VCC ──┬──[C1: 100nF]── GND
      └──[C2: 10µF]─── GND
```

### 7. External Connector

**Option A: RJ45 Connector (J1)**
**Part:** TRP RJ45 Modular Jack with magnetics removed or -
**Pinout:**
1. NC (or +12V power if needed)
2. NC (or GND if needed)
3. A (Data+)
4. NC
5. NC
6. B (Data-)
7. NC (or shield/ground)
8. NC

**Option B: Screw Terminal Connector (J1)**
**Part:** Phoenix Contact 1757019 (3-position, 5.08mm pitch)**
**Pinout:**
1. A (Data+)
2. B (Data-)
3. GND (Signal Ground/Shield)

**Standard NMEA 0183 Connection:**
- Uses twisted pair cable (typically CAT5 or marine-grade)
- Shield/ground connection recommended for noise immunity

## Complete Schematic Description

```
                                    VCC (3.3V)
                                        |
                    +-------------------+-------------------+
                    |                   |                   |
                   [C2]                [C1]              [R3: 560Ω]
                  10µF                100nF                 |
                    |                   |                   |
    +---------------+-------------------+-------------------+
    |               |                   |                   |
    |              GND                 GND                  |
    |                                                       |
    8 VCC                                                   |
    |                          MAX3485                      |
    |              U1          (SOIC-8)                     |
    |               ┌────────────────────┐                  |
    +─────────────1─┤RO              VCC├─8                |
                    │                    │                  |
MCU_RX ◄───────────2┤RE               A ├─6────────────────+──── A Line
                    │                    │         |        |
MCU_GPIO ──────┬───3┤DE               B ├─7───┐   |    [D1: TVS]
               |    │                    │     |   |    PRTR5V0U2X
MCU_TX ────────────4┤DI             GND├─5    |   +────[RT1: 120Ω]─┐
                    └────────────────────┘     |   |                 |
                            |                  |   |                 |
                           GND                 |   └─────────────────+──── B Line
                                               |         |            |
                                               |     [R4: 560Ω]   [D2: TVS]
                                               |         |            |
                                               |        GND          GND
                                               |
                                            [SW1: Optional]
                                          (Termination Enable)

                                              To External
                                              RJ45/Screw
                                              Terminal (J1)

TX/RX LEDs:

MCU_TX_LED ──[R1: 150Ω]──┤>├── GND
                         LED1
                        (Green)

MCU_RX_LED ──[R2: 150Ω]──┤>├── GND
                         LED2
                        (Yellow)
```

## Signal Flow

### Transmit Mode (MCU to RS-485 Bus)
1. MCU sets DE/RE high (3.3V) to enable driver
2. MCU sends UART data on DI pin
3. MAX3485 converts single-ended signal to differential (A-B)
4. Differential signal passes through ESD protection to external connector
5. LED1 blinks for TX activity (controlled by MCU)

### Receive Mode (RS-485 Bus to MCU)
1. MCU sets DE/RE low (0V) to enable receiver
2. Differential signal enters through connector and ESD protection
3. MAX3485 converts differential (A-B) to single-ended on RO pin
4. MCU reads UART data from RO pin
5. LED2 blinks for RX activity (controlled by MCU)

## Baud Rate Support

The design supports standard NMEA 0183 baud rates:

### 4800 baud (NMEA 0183 Standard)
- Clock: Standard UART timing
- Rise/fall time: Not critical at this speed
- Cable length: Up to 1000m with proper termination

### 38400 baud (NMEA 0183 High Speed)
- Clock: Standard UART timing
- Rise/fall time: ~200ns typical (MAX3485)
- Cable length: Up to 300m with proper termination
- TVS capacitance critical (< 1pF preferred)

## PCB Layout Guidelines

### Trace Routing
1. **Differential Pairs (A/B):**
   - Keep traces parallel and closely coupled
   - Match lengths to within 5mm
   - Minimum trace width: 20 mil (0.5mm)
   - Spacing: 10 mil (0.25mm) for 100Ω differential impedance
   - Avoid vias if possible; use same layer routing

2. **Ground Plane:**
   - Solid ground plane under transceiver
   - Do not split ground plane under differential traces
   - Keep ground plane clearance consistent

3. **Component Placement:**
   - Place ESD protection (D1) immediately at connector
   - Place transceiver (U1) close to MCU interface
   - Place decoupling caps (C1, C2) as close to U1 VCC pin as possible
   - Termination resistor (RT1) at end of trace run

4. **Power Supply:**
   - Wide power traces (> 40 mil)
   - Star ground connection to main ground
   - Keep power traces away from signal traces

### Layer Stack (2-layer board minimum)
```
TOP:    Components, Signal traces, Power
BOTTOM: Ground plane, Return paths
```

For 4-layer board:
```
TOP:    Components, Signal traces
L2:     Ground plane
L3:     Power plane (3.3V)
BOTTOM: Ground, Secondary signals
```

## Design Validation

### Electrical Characteristics
- **Common Mode Voltage Range:** -7V to +12V (MAX3485 spec)
- **Differential Output Voltage:** ±1.5V minimum @ 54Ω load
- **Receiver Input Sensitivity:** ±200mV (compliant with RS-485)
- **Input Impedance:** 96kΩ (1/8 unit load, supports 256 transceivers)

### NMEA 0183 Compliance
- **Signal Levels:** RS-422 differential (±2V to ±6V typical)
- **Baud Rates:** 4800 and 38400 supported
- **Data Format:** 8 data bits, no parity, 1 stop bit (8N1)
- **Connector:** Standard 2-wire + ground/shield
- **Termination:** 120Ω differential termination at ends

## Notes

1. **Direction Control:** The DE and RE pins should be tied together and controlled by a single MCU GPIO. Set high for transmit, low for receive.

2. **Half-Duplex Operation:** Only one device should transmit at a time. Implement proper UART collision detection in firmware.

3. **Bus Topology:** Use daisy-chain topology. Star connections cause reflections and signal integrity issues.

4. **Cable Requirements:**
   - 120Ω characteristic impedance twisted pair
   - Marine-grade if for marine applications (tinned copper, UV resistant)
   - Shielded cable recommended for noisy environments

5. **Testing Points:** Add test points for A, B, and GND signals for troubleshooting.

6. **EMI Considerations:** The MAX3485 has built-in slew rate limiting. For additional EMI reduction, consider:
   - Ferrite bead on VCC
   - Common-mode choke on A/B lines
   - Better shielding and grounding

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-10-21 | Design Team | Initial schematic design |
