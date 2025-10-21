# Hardware Design Documentation

This directory contains all hardware design documentation for the NMEA Connection Tester device.

## Directory Structure

```
hardware/
├── schematics/          # Circuit schematics and design documents
├── pcb/                 # PCB layout files and guidelines
├── bom/                 # Bill of Materials
├── datasheets/          # Component datasheets (to be added)
└── README.md            # This file
```

## Current Design: NMEA 2000 CAN Bus Interface

### Overview
Complete design documentation for a CAN bus interface compatible with NMEA 2000 specifications. This includes circuits for CAN communication, power extraction from the NMEA 2000 bus, and all necessary protection and support circuitry.

### Quick Links

#### 📋 Main Documentation
**[Complete Design Overview](../docs/hardware/can-interface.md)**
- Executive summary
- System architecture
- Technical specifications
- Implementation roadmap
- Quick reference guide

#### 🔌 Circuit Designs

**[CAN Interface Schematic](schematics/can-interface-schematic.md)**
- CAN transceiver circuits (MCP2551, TJA1050, SN65HVD230)
- Switchable 120Ω termination
- Optional galvanic isolation
- Pin connections and component values

**[Power Extraction Circuit](schematics/power-extraction-circuit.md)**
- Buck converter (9-16V → 5V)
- LDO regulator (5V → 3.3V)
- Input protection
- Thermal management

**[Connector Specification](schematics/connector-specification.md)**
- DeviceNet Micro-C (M12) connector
- Screw terminal alternative
- Pin assignments and wiring
- Installation guidelines

#### 📐 PCB Design

**[Layout Guidelines](pcb/layout-guidelines.md)**
- 2-layer and 4-layer stack-up
- Component placement strategy
- Trace routing rules
- EMC design features
- Manufacturing specifications

#### 🛒 Components

**[Bill of Materials](bom/can-interface-bom.csv)**
- Complete component list
- Part numbers and suppliers
- Multiple vendor options
- Pricing information
- Alternative parts

## Design Status

| Component | Status | Notes |
|-----------|--------|-------|
| CAN Interface Schematic | ✅ Complete | Multiple transceiver options documented |
| Power Supply Design | ✅ Complete | Buck + LDO with protection |
| PCB Layout Guidelines | ✅ Complete | 2-layer and 4-layer options |
| Connector Spec | ✅ Complete | Micro-C and screw terminal options |
| Bill of Materials | ✅ Complete | All components with part numbers |
| PCB Layout (CAD) | ⏳ Pending | Next step: Create in KiCAD/Eagle |
| Prototype PCBs | ⏳ Pending | Waiting for layout completion |
| Component Datasheets | ⏳ Pending | To be collected in datasheets/ |

## Key Features

- ✅ NMEA 2000 compliant (250 kbps CAN)
- ✅ Bus-powered (9-16V input)
- ✅ Switchable 120Ω termination
- ✅ Multiple connector options
- ✅ Optional galvanic isolation
- ✅ EMC protection
- ✅ Cost-effective design (~$18-28)

## Getting Started

### For Hardware Developers

1. **Review Design**
   - Start with [Main Documentation](../docs/hardware/can-interface.md)
   - Review each schematic document
   - Check PCB layout guidelines

2. **Source Components**
   - Review [BOM](bom/can-interface-bom.csv)
   - Order components from preferred supplier
   - Consider alternatives if needed

3. **Create PCB Layout**
   - Use KiCAD or Eagle
   - Follow [Layout Guidelines](pcb/layout-guidelines.md)
   - Place components per recommendations

4. **Order PCBs**
   - Export Gerber files
   - Order from JLCPCB, PCBWay, or OSH Park
   - Consider assembly service for prototypes

5. **Assemble and Test**
   - Follow assembly checklist
   - Perform electrical testing
   - Validate NMEA 2000 communication

### For Firmware Developers

**Key Information**:
- CAN TX/RX pins configurable (example: GPIO 4/5 on ESP32)
- Power outputs: 5V @ 500mA, 3.3V @ 800mA
- CAN data rate: 250 kbps (fixed)
- Use NMEA 2000 library (e.g., ttlappalainen/NMEA2000)

**See**: [Main Documentation - Quick Reference](../docs/hardware/can-interface.md#quick-reference)

## Technical Specifications

### Input
- Voltage: 9-16V DC (NMEA 2000 bus)
- Current: < 50mA @ 12V (< 1 LEN)
- Protection: TVS, reverse polarity, overcurrent

### CAN Interface
- Standard: NMEA 2000 / ISO 11898
- Data rate: 250 kbps
- Termination: 120Ω (switchable)
- Connector: Micro-C or screw terminal

### Power Output
- 5V rail: 500mA max
- 3.3V rail: 800mA max
- Efficiency: >85%

## Cost Estimate

| Configuration | Estimated Cost |
|---------------|----------------|
| Base circuit (screw terminal) | ~$18 |
| With Micro-C connector | ~$28 |
| With galvanic isolation | ~$21 |
| Production (100+ units) | ~$15-20 |

*Prices are estimates based on single-unit purchases*

## Next Steps

### Immediate Tasks
1. [ ] Review design documentation
2. [ ] Create PCB layout in KiCAD/Eagle
3. [ ] Order prototype PCBs (5-10 pcs)
4. [ ] Source components
5. [ ] Assemble first prototype
6. [ ] Initial testing and validation

### Future Enhancements
- [ ] Add component datasheets to datasheets/
- [ ] Create 3D PCB models
- [ ] Generate assembly instructions
- [ ] Add example firmware code
- [ ] Create testing procedures document

## Design Tools

### Recommended Software
- **PCB Design**: KiCAD (free, open-source)
- **Simulation**: LTspice (free, power supply)
- **CAD Models**: SnapEDA, Ultra Librarian
- **Gerber Viewer**: gerbv, KiCAD viewer

### PCB Manufacturers
- **JLCPCB**: Low cost, assembly service
- **PCBWay**: Good quality, assembly service
- **OSH Park**: High quality, US-based
- **ALLPCB**: Budget option

## Support

### Documentation Issues
If you find errors or have suggestions for the documentation:
1. Open an issue on GitHub
2. Tag it with "documentation" label
3. Provide specific details

### Design Questions
For questions about the hardware design:
1. Check the main documentation first
2. Review relevant schematic documents
3. Open a discussion on GitHub
4. Tag with "hardware" label

### Component Sourcing
For component availability issues:
1. Check BOM for alternative parts
2. Search on Octopart.com for availability
3. Consider pin-compatible alternatives
4. Ask in GitHub discussions

## Contributing

Improvements to the hardware design are welcome! Please:
1. Review existing documentation
2. Make changes in a feature branch
3. Update related documents
4. Test changes if possible
5. Submit pull request with clear description

### Areas for Contribution
- PCB layout creation (KiCAD/Eagle)
- Component datasheets collection
- Alternative component suggestions
- Cost optimization ideas
- EMC testing and improvements
- International sourcing options

## References

### Standards
- NMEA 2000 (IEC 61162-3)
- ISO 11898 (CAN specification)
- IEC 61076-2-101 (M12 connector)

### Resources
- NMEA website: www.nmea.org
- CAN in Automation: www.can-cia.org
- NMEA 2000 library: github.com/ttlappalainen/NMEA2000

## License

See main repository LICENSE file for terms.

---

**Hardware Design Status**: Documentation Complete ✅

**Last Updated**: 2025-10-21

**Design Version**: 1.0

For the latest information, see the [main documentation](../docs/hardware/can-interface.md).
