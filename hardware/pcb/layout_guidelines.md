# PCB Layout Design for RS-422/RS-485 Interface

## Overview
This document provides detailed PCB layout guidelines for the NMEA 0183 RS-422/RS-485 interface section, ensuring optimal signal integrity, EMI performance, and reliability.

## Board Specifications

### General Requirements
- **Board Size:** 50mm x 30mm (interface section)
- **Board Thickness:** 1.6mm standard FR-4
- **Copper Weight:** 1 oz (35µm) on all layers
- **Minimum Layer Count:** 2 layers (4 layers recommended for better performance)
- **Surface Finish:** ENIG (Electroless Nickel Immersion Gold) or HASL lead-free
- **Solder Mask:** Green (or blue for marine applications)
- **Silkscreen:** White, both sides

### Design Rules
- **Minimum Track Width:** 6 mil (0.15mm) for signals, 20 mil (0.5mm) for power
- **Minimum Track Spacing:** 6 mil (0.15mm)
- **Minimum Via Size:** 12 mil (0.3mm) drill, 24 mil (0.6mm) pad
- **Differential Pair Impedance:** 100Ω ±10%
- **Single-ended Impedance:** 50Ω ±10%

## Component Placement

### Layout Flow
```
[External Connector J1] → [ESD Protection D1,D2] → [Transceiver U1] → [MCU Interface]
         ↓
   [Termination RT1/SW1]
         ↓
   [Status LEDs LED1,LED2]
```

### Critical Component Placement Order

#### 1. External Connector (J1)
**Position:** Edge of board
**Orientation:** Facing outward for cable access
**Clearance:** 
- 5mm from board edge to connector pins
- 10mm keepout area behind connector for cable strain relief

**Placement Notes:**
- If using RJ45: Ensure jack is parallel to board edge
- If using screw terminal: Position terminals perpendicular to board
- Add mounting holes near connector for mechanical support

#### 2. ESD Protection Diodes (D1, D2)
**Position:** Immediately adjacent to connector J1 (< 5mm trace length)
**Orientation:** Between connector and transceiver
**Package:** SOT-143 or equivalent small footprint

**Critical Requirements:**
- Minimize trace length from connector to ESD device
- Direct path to ground plane via multiple vias
- Place on same side as connector

#### 3. Termination Network (RT1, SW1)
**Position:** Close to connector, parallel to differential lines
**Components:**
- RT1 (120Ω resistor): 0805 package
- SW1 (optional switch): 2-pin header or DIP switch

**Placement:**
- Between A and B lines at entry point
- If using switch, orient for easy access after assembly
- Alternative: Place jumper on edge for external access

#### 4. Differential Transceiver (U1 - MAX3485)
**Position:** Central location between connector and MCU interface
**Orientation:** A/B pins facing connector side, DI/RO facing MCU side
**Package:** SOIC-8

**Critical Requirements:**
- 15-20mm from connector (after ESD protection)
- Keep VCC and GND pins close to decoupling capacitors
- Align for short traces to both connector and MCU interface

#### 5. Decoupling Capacitors (C1, C2)
**Position:** Immediately adjacent to U1 VCC pin (< 3mm)
**Components:**
- C1 (100nF): 0805 ceramic, place closest to VCC pin
- C2 (10µF): 0805 ceramic, place next to C1

**Placement:**
- Same side as transceiver
- Via to ground plane directly under capacitor ground pad
- Star connection to U1 ground pin

#### 6. Bias Resistors (R3, R4)
**Position:** Between transceiver and termination network
**Components:**
- R3 (560Ω): 0805, connects A line to VCC
- R4 (560Ω): 0805, connects B line to GND

**Placement:**
- Along differential pair routing
- Close to transceiver side
- Minimize stub length

#### 7. Status LEDs (LED1, LED2)
**Position:** Visible location on board edge or top surface
**Components:**
- LED1 (TX - Green): 0805 or through-hole
- LED2 (RX - Yellow): 0805 or through-hole
- R1, R2 (150Ω): 0805 current limiting resistors

**Placement:**
- Near MCU interface side
- Visible from front panel
- Space LEDs 5mm apart minimum
- Orient consistently (cathode toward ground)

## Layer Stackup

### 2-Layer Board Configuration

#### Top Layer (Component Side)
```
- All SMD components
- Signal traces (A, B differential pair)
- Power traces (VCC 3.3V)
- MCU interface traces (TX, RX, DE/RE)
- LED drive traces
- Pour copper fill tied to GND (hatched, 30% coverage)
```

#### Bottom Layer (Ground Side)
```
- Solid ground plane (primary return path)
- Minimal signal traces if necessary
- Through-hole component soldering
- Via stitching around board perimeter
```

### 4-Layer Board Configuration (Recommended)

#### Layer 1 (Top - Component Side)
```
- All SMD components  
- Signal traces (minimal, controlled impedance)
- Short high-speed connections
- Component silkscreen
```

#### Layer 2 (Inner - Ground Plane)
```
- Solid ground plane
- No splits or cuts under high-speed signals
- Connected to all ground pins and pads
```

#### Layer 3 (Inner - Power Plane)
```
- 3.3V power plane
- Thermal reliefs to power pins
- Keepout under sensitive analog circuits
```

#### Layer 4 (Bottom)
```
- Ground plane (secondary)
- Additional signal routing if needed
- Component silkscreen
```

## Critical Trace Routing

### Differential Pair Routing (A and B Lines)

#### Routing Parameters
```
Impedance Target: 100Ω differential
Trace Width: 20 mil (0.5mm)
Trace Spacing: 10 mil (0.25mm) between A and B
Length Matching: ±5mm maximum skew
```

#### Routing Rules
1. **Coupling:** Keep A and B traces parallel and closely coupled
2. **Symmetry:** Route both traces identically (same bends, same layer)
3. **Length Matching:** Use serpentine routing if needed to match lengths
4. **Via Usage:** Avoid vias in differential pair if possible
   - If vias required, use vias on both traces simultaneously
   - GND via stitching on both sides of differential via
5. **Layer Changes:** Keep both traces on same layer throughout
6. **Clearance:** Maintain 3x spacing (30 mil) to other signals

#### Routing Path
```
Connector J1 → ESD D1 → Termination RT1 → Transceiver U1 (A/B pins)
                |
            GND vias
```

**Implementation:**
```
   A line:  J1.3 ──══════════════════════════ U1.6
                     ║                    ║
   B line:  J1.6 ──══════════════════════════ U1.7
                 ║       RT1      ║
                 ║    120Ω   ║
                  ══════════════
```

### Single-Ended Signal Routing

#### MCU Interface Signals (DI, RO, DE/RE)
- **Trace Width:** 10 mil (0.25mm)
- **Impedance:** 50Ω single-ended
- **Length:** Keep < 50mm
- **Routing:** Direct point-to-point, avoid meanders
- **Ground:** Route over solid ground plane
- **Clearance:** 20 mil from power traces, 10 mil from other signals

#### Power Supply Traces (VCC)
- **Trace Width:** 40 mil (1.0mm) minimum
- **Current Capacity:** 35 mA/mil for 1oz copper
- **Routing:** Short, direct path from power input to U1
- **Decoupling:** Connect C1 and C2 directly to VCC trace
- **Via Size:** Use multiple vias for power connections (thermal relief)

#### Ground Connections
- **Strategy:** Star grounding for analog/digital separation
- **Via Stitching:** Place ground vias every 10mm around board perimeter
- **Return Path:** Ensure ground return path follows signal path
- **Connection:** Direct via to ground plane (no thermal relief for signals)

### LED Drive Traces
- **Trace Width:** 15 mil (0.4mm)
- **Length:** Keep reasonable, not critical
- **Current:** Design for 20mA continuous
- **Routing:** Can cross other traces if necessary (low speed)

## Grounding Strategy

### Ground Plane Design

#### Single Point Ground (Star Ground)
```
                    Main GND
                        |
        ┌───────────────┼───────────────┐
        |               |               |
    Digital GND     Signal GND      Chassis GND
    (MCU side)    (Transceiver)     (Connector)
        |               |               |
    [Via Array]    [Via Array]    [Via Array]
```

#### Implementation
1. **Main Ground Node:** Central connection point for all ground returns
2. **Signal Ground:** Transceiver, ESD protection, termination ground
3. **Digital Ground:** MCU interface, LED grounds
4. **Chassis Ground:** Optional connection to enclosure/shield
5. **Via Stitching:** Place 6-8 vias at each ground node

### Ground Via Placement
- **Decoupling Caps:** Via directly under ground pad
- **ESD Protection:** Multiple vias (4-6) for fast transient return
- **Transceiver:** Via array around IC perimeter
- **Differential Traces:** Via stitching on both sides (every 20mm)

## Thermal Management

### Heat Dissipation
- **MAX3485:** Low power dissipation (~1mW typical)
- **Heat Sink:** Not required
- **Thermal Vias:** Optional, but recommended under U1 for reliability
- **Copper Pour:** Use copper pour under U1 connected to ground for heat spreading

### Temperature Considerations
- **Operating Range:** -40°C to +85°C (industrial temp range)
- **Board Material:** Standard FR-4 adequate (Tg > 130°C)
- **Component Rating:** Ensure all components rated for target environment

## EMI/EMC Design

### EMI Reduction Techniques

#### 1. Shielding
- **Ground Plane:** Solid, unbroken ground plane under all signals
- **Guard Traces:** Ground traces surrounding differential pair
- **Via Shielding:** Via fence around sensitive circuits

#### 2. Filtering
- **Decoupling:** Capacitors at power pins (C1, C2)
- **Ferrite Beads:** Optional on VCC line for additional filtering
- **Common-Mode Choke:** Optional on A/B lines for severe EMI environments

#### 3. Layout
- **Trace Length:** Minimize all high-speed trace lengths
- **Loop Area:** Minimize loop area of signal and return paths
- **Edge Spacing:** Keep high-speed signals 20mm from board edge
- **Via Stitching:** Create EM barrier around high-speed circuits

### ESD Protection Layout
```
External World → Connector J1 → [ESD TVS] → Protected Circuit
                                    ↓
                              [Ground Vias]
                                    ↓
                              Ground Plane
```

**Critical Points:**
- ESD current path must be low impedance
- Use multiple large vias (0.4mm drill minimum)
- Keep trace length from connector to TVS < 5mm
- Ground plane directly under ESD path

## Manufacturing Considerations

### Assembly Notes
1. **Component Side:** All SMD components on top side
2. **Through-Hole:** J1 connector may be through-hole
3. **Reflow Profile:** Standard lead-free profile for SMD
4. **Hand Soldering:** Provide test points for manual rework

### Design for Manufacturing (DFM)

#### Minimum Features
- **Annular Ring:** 4 mil minimum
- **Pad Size:** Drill diameter + 12 mil
- **Solder Mask:** 4 mil clearance around pads
- **Silkscreen:** 6 mil line width, 8 mil text height

#### Panel Array
- **Panelization:** 2x2 or 3x3 array for volume production
- **Breakaway Tabs:** V-groove or mouse-bites
- **Fiducials:** 3 fiducials per board for automated assembly
- **Tooling Holes:** 4 corners, 3mm diameter

### Testing and Debug

#### Test Points
Add test points (TP) for key signals:
- TP1: A line (differential positive)
- TP2: B line (differential negative)
- TP3: Ground reference
- TP4: VCC (3.3V power)
- TP5: DI (transmit data)
- TP6: RO (receive data)

**Test Point Specifications:**
- Type: SMD pad or through-hole loop
- Size: 40 mil (1.0mm) diameter minimum
- Clearance: 100 mil from adjacent test points
- Access: Top side, clearly labeled

#### Debug Header (Optional)
Add 6-pin header for logic analyzer connection:
1. GND
2. DI (MCU TX)
3. RO (MCU RX)
4. DE/RE (direction control)
5. A (differential +)
6. B (differential -)

## Documentation on PCB

### Silkscreen Information
- **Component Designators:** All components clearly marked
- **Polarity:** Mark polarity for LEDs, diodes, electrolytic caps
- **Pin 1:** Mark pin 1 for all ICs
- **Connector:** Pin numbering for J1
- **Test Points:** Label all test points (TP1-TP6)
- **Version:** Board revision number (e.g., "Rev 1.0")
- **Title:** "RS-485 NMEA Interface"

### Assembly Drawing
Include on silkscreen or in assembly document:
- Component placement outline
- Orientation marks for polarized components
- Jumper settings for termination enable/disable
- LED function labels (TX, RX)

## Design Verification Checklist

### Pre-Production Checks
- [ ] Differential impedance calculated and verified (100Ω)
- [ ] Trace lengths matched (A and B within 5mm)
- [ ] All components have correct footprints
- [ ] Decoupling capacitors placed correctly (< 3mm from IC)
- [ ] ESD protection placed at connector entry (< 5mm)
- [ ] Ground vias adequate (multiple vias at critical nodes)
- [ ] Power trace widths adequate for current (40 mil minimum)
- [ ] Clearances meet manufacturing minimums (6 mil)
- [ ] Test points provided for all critical signals
- [ ] Fiducials placed for automated assembly (if applicable)
- [ ] Board revision marked on silkscreen
- [ ] All components available from suppliers

### DRC (Design Rule Check)
- [ ] No unconnected nets
- [ ] No clearance violations
- [ ] No minimum trace width violations
- [ ] No minimum spacing violations
- [ ] No minimum annular ring violations
- [ ] No acid traps or acute angles in traces

### Simulation (Optional but Recommended)
- [ ] SPICE simulation of differential signaling
- [ ] SI (Signal Integrity) analysis at 38400 baud
- [ ] EMI simulation for radiated emissions
- [ ] Thermal simulation for worst-case power dissipation

## Layout Example Description

### Top Layer Visualization
```
     J1 (RJ45/Terminal)
         |
    [D1:TVS]
         |
      ┌──┴──┐
      │ RT1 │ [SW1]
      └──┬──┘
         │
    ═════╪═════  A line (differential +)
    ═════╪═════  B line (differential -)
         │
      ┌──┴──┐
      │ U1  │ MAX3485
      │SOIC8│
      └──┬──┘
         │
    ┌────┼────┐
    │ C1   C2 │
    └────┼────┘
         │
    [MCU Interface]
      RX TX DE
         │
    [LED1] [LED2]
```

### Cross-Section View
```
Top:     Components | Signals | Power traces
         ------------------------------------------------
L2/GND:  ████████████████ GROUND PLANE ████████████████
         ------------------------------------------------
L3/PWR:  ░░░░░░░░░ 3.3V POWER PLANE ░░░░░░░░░░░░░░
         ------------------------------------------------
Bottom:  Ground plane | Secondary signals
```

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-10-21 | Design Team | Initial PCB layout guidelines |

## References
- IPC-2221: Generic Standard on Printed Board Design
- IPC-2222: Sectional Design Standard for Rigid Organic Printed Boards
- IPC-7351: Generic Requirements for Surface Mount Design and Land Pattern Standard
- RS-485 PCB Layout Guidelines (TI Application Note SLLA414)
- High-Speed Layout Guidelines (Analog Devices Tutorial MT-094)
