# NMEA 2000 CAN Bus Interface - Complete Design Documentation

## Executive Summary

This document provides comprehensive design documentation for a CAN bus interface compatible with NMEA 2000 marine communication standard. The design includes a complete hardware solution for integrating NMEA 2000 connectivity into the portable NMEA connection tester device.

### Key Features
- ✅ NMEA 2000 compliant (250 kbps CAN bus)
- ✅ Multiple CAN transceiver options (MCP2551, TJA1050, SN65HVD230)
- ✅ Switchable 120Ω termination resistor
- ✅ Bus-powered operation (9-16V input from NMEA 2000 bus)
- ✅ Regulated 5V and 3.3V outputs
- ✅ DeviceNet Micro-C (M12) connector or screw terminal options
- ✅ Optional galvanic isolation
- ✅ Input protection (TVS diode, resettable fuse)
- ✅ EMC design considerations

## Design Overview

### System Architecture
```
┌─────────────────────────────────────────────────────────────────────┐
│                    NMEA 2000 CAN Interface                           │
│                                                                       │
│  NMEA 2000 Bus (9-16V)                                               │
│         │                                                             │
│         ├──[Protection]──[Buck 9-16V→5V]──[LDO 5V→3.3V]             │
│         │                      │                │                    │
│         │                      ↓                ↓                    │
│         │                  5V Logic         3.3V Logic               │
│         │                      │                │                    │
│         │                      ↓                ↓                    │
│  ┌──────▼──────┐     ┌────────▼────────────────▼────────┐          │
│  │  Micro-C    │     │        MCU (ESP32)                │          │
│  │ Connector   │     │    - CAN Controller (built-in)    │          │
│  │             │     │    - Processing                   │          │
│  │ CAN_H ──────┼─────┤    - Display/UI                   │          │
│  │ CAN_L ──────┼─────┤                                   │          │
│  │             │     └───────────────┬───────────────────┘          │
│  │ V+ (12V) ───┼─────────────────────┘                              │
│  │ GND ────────┼────────────────────────────────────────────────┐   │
│  │ Shield ─────┼────────────────────────────────────────────────┤   │
│  └─────────────┘                                                  │   │
│         │                                                          │   │
│         ▼                                                          ▼   │
│  ┌──────────────┐        ┌──────────────┐              ┌──────────┐ │
│  │CAN Transceiver│        │   Optional   │              │  Ground  │ │
│  │  MCP2551 or  │◄───────┤  Isolator    │              │  Plane   │ │
│  │  SN65HVD230  │        │  ADUM1201    │              │          │ │
│  └──────────────┘        └──────────────┘              └──────────┘ │
│         │                                                             │
│         ▼                                                             │
│  [120Ω Termination]                                                  │
│     (Switchable)                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Design Documents

This complete design package includes the following documentation:

### 1. Schematic Design
**Location**: `hardware/schematics/can-interface-schematic.md`

**Contents**:
- Complete circuit schematics for CAN transceiver interface
- Three transceiver options (MCP2551, TJA1050, SN65HVD230)
- Switchable termination resistor circuit
- Optional galvanic isolation using ADUM1201
- Pin connections for ESP32 MCU
- Component values and specifications
- Testing points

**Key Design Decisions**:
- **Recommended transceiver**: SN65HVD230 for 3.3V systems (ESP32 compatible)
- **Alternative**: MCP2551 for 5V systems (lower cost, widely available)
- **Termination**: 120Ω switchable via jumper or switch
- **Data rate**: 250 kbps (NMEA 2000 standard)

### 2. Power Supply Design
**Location**: `hardware/schematics/power-extraction-circuit.md`

**Contents**:
- Buck converter design (9-16V input → 5V output)
- LDO regulator design (5V → 3.3V)
- Three buck converter options (LM2596, MP1584, TPS54331)
- Input protection circuit (TVS diode, resettable fuse)
- Reverse polarity protection
- Power sequencing
- Thermal management

**Key Design Decisions**:
- **Recommended**: MP1584 module for prototypes (pre-assembled, high efficiency)
- **Production**: TPS54331 for best efficiency (95%) and small size
- **Power budget**: < 1 LEN (50mA @ 12V) for NMEA 2000 compliance
- **Protection**: SMBJ18CA TVS diode, 2A polyfuse

### 3. PCB Layout Guidelines
**Location**: `hardware/pcb/layout-guidelines.md`

**Contents**:
- 2-layer and 4-layer PCB stack-up recommendations
- Component placement strategy
- Trace width and spacing requirements
- Ground plane design
- CAN differential pair routing
- Power distribution network (PDN)
- EMC design features
- Manufacturing specifications

**Key Design Guidelines**:
- **CAN traces**: 10 mil width, 10 mil spacing, matched length
- **Differential impedance**: 120Ω ± 10%
- **Ground plane**: Solid bottom layer, stitched vias
- **Power traces**: Adequate width for current (>20 mil for 500mA)
- **EMC**: Via stitching, common mode choke, TVS protection

### 4. Connector Specification
**Location**: `hardware/schematics/connector-specification.md`

**Contents**:
- DeviceNet Micro-C (M12) connector specification
- Screw terminal alternative specification
- Pin assignments and color coding
- Mechanical drawings and footprints
- Cable specifications
- Installation guidelines
- Sourcing information and part numbers

**Key Connector Options**:
- **Option 1 (Recommended)**: Phoenix Contact SACC-M12MS-5CON-PG9 Micro-C connector
- **Option 2 (Cost-effective)**: Wurth Elektronik 5.08mm screw terminal
- **Option 3 (Debug)**: 2x5 pin 0.1" header for development

### 5. Bill of Materials
**Location**: `hardware/bom/can-interface-bom.csv`

**Contents**:
- Complete component list with part numbers
- Multiple vendor options (Digi-Key, Mouser, LCSC)
- Alternative parts for each critical component
- Pricing information
- Package specifications
- Sourcing recommendations

**Cost Summary**:
- Base circuit (screw terminal): ~$18
- With Micro-C connector: ~$28
- With isolation: ~$21
- Production cost (100+ units): ~$15-20

## Technical Specifications

### Electrical Specifications

#### Input
- **Voltage Range**: 9-16V DC (nominal 12V)
- **Current Draw**: < 50mA @ 12V (< 1 LEN)
- **Protection**: Reverse polarity, overvoltage (18V TVS), overcurrent (2A fuse)

#### CAN Interface
- **Standard**: NMEA 2000 (IEC 61162-3), ISO 11898
- **Data Rate**: 250 kbps (fixed)
- **Voltage Levels**: 
  - Recessive: CAN_H = 2.5V, CAN_L = 2.5V
  - Dominant: CAN_H = 3.5V, CAN_L = 1.5V
- **Termination**: 120Ω (switchable)
- **Cable**: Twisted pair, 120Ω characteristic impedance

#### Power Outputs
- **5V Rail**: 5V ± 5%, 500mA max, <50mV ripple
- **3.3V Rail**: 3.3V ± 5%, 800mA max, <50mV ripple
- **Efficiency**: >85% (buck converter), >80% (total)

### Physical Specifications
- **Connector**: M12 5-pin A-coded (Micro-C) or 5-position screw terminal
- **PCB Size**: TBD (typically 50x30mm for this circuit)
- **Operating Temperature**: -40°C to +85°C
- **IP Rating**: IP67 (when mated with proper connector)

### Compliance
- **NMEA 2000**: IEC 61162-3
- **CAN**: ISO 11898-2
- **EMC**: Basic EMC design practices implemented
- **Safety**: Input protection, galvanic isolation (optional)

## Design Validation

### Acceptance Criteria Status

✅ **CAN Interface Schematic**: Complete
- Detailed schematic with three transceiver options
- Switchable termination circuit
- Optional isolation circuit
- Test point locations

✅ **Power Extraction Circuit**: Complete
- Buck converter design (9-16V → 5V)
- LDO regulator (5V → 3.3V)
- Input protection circuit
- Thermal management guidelines

✅ **PCB Layout Guidelines**: Complete
- 2-layer and 4-layer stack-up
- Component placement strategy
- Trace routing guidelines
- EMC design features

✅ **Connector Specification**: Complete
- Micro-C connector details and part numbers
- Screw terminal alternative
- Pin assignments and cable specs
- Installation guidelines

### Testing and Validation

#### Prototype Testing Checklist
- [ ] Power supply voltage regulation (5V, 3.3V within ±5%)
- [ ] Current consumption < 50mA @ 12V
- [ ] CAN bus communication at 250 kbps
- [ ] Termination resistance measurement (120Ω ± 1%)
- [ ] Signal levels on CAN_H and CAN_L
- [ ] Input protection (reverse polarity, overvoltage)
- [ ] EMC pre-compliance testing
- [ ] Temperature testing (-40°C to +85°C)

#### NMEA 2000 Certification
For commercial products:
1. Join NMEA organization
2. Submit design for review
3. Pass certification testing
4. Obtain NMEA 2000 certification number
5. Use certified logo on product

**Note**: Certification is required for commercial marine products but not for DIY/prototype devices.

## Implementation Roadmap

### Phase 1: Prototype Development (Current)
- [x] Complete schematic design
- [x] Document power supply circuit
- [x] Define PCB layout guidelines
- [x] Specify connectors
- [ ] Create PCB layout in KiCAD/Eagle
- [ ] Order prototype PCBs
- [ ] Source components

### Phase 2: Initial Testing
- [ ] Assemble prototype boards
- [ ] Basic functionality testing
- [ ] Power supply validation
- [ ] CAN communication testing
- [ ] EMC pre-compliance checks

### Phase 3: Integration
- [ ] Integrate with ESP32 firmware
- [ ] Test with actual NMEA 2000 devices
- [ ] Validate message parsing
- [ ] User interface integration
- [ ] Field testing

### Phase 4: Production Preparation
- [ ] Refine PCB layout based on testing
- [ ] Create manufacturing documentation
- [ ] Establish supply chain
- [ ] Prepare assembly instructions
- [ ] Plan NMEA 2000 certification (if commercial)

## Development Tools and Resources

### PCB Design Software
- **KiCAD**: Free, open-source (recommended for open hardware)
- **Eagle**: Popular, free version available
- **Altium**: Professional (expensive)

### Simulation Tools
- **LTspice**: Free, for power supply simulation
- **TINA-TI**: Free, Texas Instruments simulator
- **PSPICE**: Professional simulation

### Reference Libraries
- **KiCAD Libraries**: Standard component libraries
- **SnapEDA**: Free CAD models and footprints
- **Ultra Librarian**: Component library tool

### Datasheets
Store component datasheets in: `hardware/datasheets/`
- MCP2551: CAN transceiver
- SN65HVD230: 3.3V CAN transceiver
- TJA1050: Automotive CAN transceiver
- LM2596: Buck converter
- MP1584: Buck converter module
- TPS54331: High-efficiency buck converter
- AMS1117: LDO regulator
- ADUM1201: Digital isolator
- SMBJ18CA: TVS diode

## Next Steps

### Immediate Actions
1. **Review Design**: Have design reviewed by experienced hardware engineer
2. **Create PCB Layout**: Use KiCAD to create physical PCB layout
3. **Simulation**: Simulate power supply circuit in LTspice
4. **Component Sourcing**: Order components from preferred suppliers
5. **Prototype PCB**: Order PCBs from JLCPCB, PCBWay, or OSH Park

### Development Considerations
1. **MCU Selection**: Design assumes ESP32 with built-in CAN controller
2. **Firmware**: Need to implement NMEA 2000 protocol stack
3. **Testing Equipment**: Need CAN bus analyzer (USB-CAN adapter)
4. **Test Devices**: Access to NMEA 2000 devices for testing

### Risk Mitigation
1. **Component Availability**: Multiple alternatives specified for each component
2. **Design Verification**: Extensive documentation allows peer review
3. **Cost Control**: Budget-friendly alternatives provided
4. **Scalability**: Design suitable for both prototype and production

## Support and Resources

### NMEA 2000 Resources
- **NMEA Website**: www.nmea.org
- **NMEA 2000 Standard**: Purchase from NMEA or IEC
- **Developer Forum**: www.cruisersforum.com
- **Open Source**: github.com/ttlappalainen/NMEA2000

### Community Support
- **GitHub Repository**: aayaffe/nmea-connection-tester
- **Issue Tracker**: For bug reports and feature requests
- **Discussions**: For questions and community support

### Commercial Support
- **Hardware Design Review**: Consider hiring RF/hardware consultant
- **PCB Assembly**: JLCPCB, PCBWay offer assembly services
- **Certification Assistance**: NMEA offers certification support

## Revision History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2025-10-21 | Copilot Agent | Initial design documentation |
| | | | Complete schematic, power, PCB, connector specs |
| | | | Bill of materials with sourcing information |

## License and Usage

This design is part of the NMEA Connection Tester project. Please refer to the repository license for usage terms.

**Design Status**: Documentation Complete, PCB Layout Pending

**Recommended for**: Prototype development, design review, component sourcing

**Not Recommended for**: Direct manufacturing without further validation and testing

---

## Quick Reference

### File Structure
```
hardware/
├── schematics/
│   ├── can-interface-schematic.md       # CAN transceiver circuit
│   ├── power-extraction-circuit.md      # Power supply design
│   └── connector-specification.md       # Connector details
├── pcb/
│   └── layout-guidelines.md             # PCB layout rules
├── bom/
│   └── can-interface-bom.csv           # Bill of materials
└── datasheets/                          # Component datasheets (TBD)

docs/
└── hardware/
    └── can-interface.md                 # This file
```

### Key Contact Pins (ESP32 Example)
| Function | ESP32 Pin | CAN Transceiver | Notes |
|----------|-----------|-----------------|-------|
| CAN TX | GPIO 4 | TXD (Pin 1) | Configurable |
| CAN RX | GPIO 5 | RXD (Pin 4) | Configurable |
| Power | 5V | VDD (Pin 3) | For 5V transceiver |
| Power | 3.3V | VDD (Pin 3) | For 3.3V transceiver |
| Ground | GND | VSS (Pin 2) | Common ground |

### Quick Start for Developers
1. Read this document for overview
2. Review schematic in `can-interface-schematic.md`
3. Check power supply in `power-extraction-circuit.md`
4. Review PCB guidelines in `layout-guidelines.md`
5. Source components from `can-interface-bom.csv`
6. Create PCB layout following guidelines
7. Order PCBs and components
8. Assemble and test following test checklist

---

**Document Status**: ✅ COMPLETE - Ready for design review and prototype development

For questions or issues, please open an issue on GitHub or contact the project maintainer.
