# Hardware Documentation

This directory contains the complete hardware design documentation for the NMEA Connection Tester, specifically focusing on the RS-422/RS-485 interface for NMEA 0183 compatibility.

## Directory Structure

```
hardware/
├── schematics/          # Circuit schematics and design documents
├── pcb/                 # PCB layout guidelines and design files
├── bom/                 # Bill of Materials with supplier information
├── datasheets/          # Component datasheets and compliance documents
└── README.md            # This file
```

## Quick Start

### For Engineers Implementing the Design

1. **Review the Schematic**: Start with `schematics/rs422_rs485_interface.md` to understand the circuit design
2. **PCB Layout**: Follow guidelines in `pcb/layout_guidelines.md` for proper implementation
3. **Order Components**: Use `bom/rs485_interface_bom.md` for sourcing all required parts
4. **Testing**: Follow `datasheets/compliance_verification_plan.md` for validation

### Key Design Features

- **Transceiver**: MAX3485ESA+ (3.3V, low power, RS-485/RS-422 compatible)
- **Baud Rates**: 4800 and 38400 baud (NMEA 0183 standard and high-speed)
- **ESD Protection**: ±12kV contact discharge protection on all external connections
- **Status Indication**: TX and RX activity LEDs
- **Termination**: Optional 120Ω termination with switch/jumper
- **Connectors**: Supports both RJ45 and screw terminal options

## Design Documents

### Schematics (`schematics/`)

**rs422_rs485_interface.md**
- Complete circuit schematic description
- Component selection rationale
- Signal flow explanation
- Pinout definitions
- Design notes and best practices

### PCB Layout (`pcb/`)

**layout_guidelines.md**
- Detailed PCB layout specifications
- Layer stackup recommendations
- Critical trace routing guidelines
- Component placement rules
- Grounding strategy
- EMI/EMC design considerations
- Manufacturing specifications

### Bill of Materials (`bom/`)

**rs485_interface_bom.md**
- Complete component list with specifications
- Supplier information and part numbers
- Pricing for prototype and production quantities
- Alternative component suggestions
- Cost analysis and optimization strategies

### Compliance and Testing (`datasheets/`)

**compliance_verification_plan.md**
- Comprehensive testing procedures
- NMEA 0183 compliance verification
- RS-422/RS-485 electrical testing
- Interoperability testing
- Environmental qualification
- Certification guidance (FCC, CE, NMEA)

## Design Specifications

### Electrical Specifications

| Parameter | Specification |
|-----------|---------------|
| **Supply Voltage** | 3.3V ±5% |
| **Current Consumption** | < 10 mA typical |
| **Differential Output** | ±2V typical @ 120Ω load |
| **Common-Mode Range** | -7V to +12V |
| **Receiver Sensitivity** | ±200 mV |
| **Data Rate** | 4800, 38400 baud (configurable) |
| **ESD Protection** | ±12kV contact, ±15kV air discharge |

### Environmental Specifications

| Parameter | Specification |
|-----------|---------------|
| **Operating Temperature** | -40°C to +85°C |
| **Storage Temperature** | -55°C to +125°C |
| **Humidity** | 85% RH @ 85°C |
| **Vibration** | 2 grms, 10-500 Hz |

### Compliance Standards

- **NMEA 0183**: Full compliance (v4.11)
- **RS-422**: TIA/EIA-422-B compatible
- **RS-485**: TIA/EIA-485-A compatible
- **IEC 61162-1**: Maritime navigation equipment
- **RoHS**: RoHS 2 compliant (all components)
- **EMC**: FCC Part 15 Class B (preliminary)

## Implementation Guide

### Step 1: Review Design

1. Read through the complete schematic documentation
2. Understand the circuit operation and signal flow
3. Review component specifications and alternatives
4. Consider any application-specific requirements

### Step 2: PCB Design

1. Follow the PCB layout guidelines strictly
2. Pay special attention to:
   - Differential pair routing (A/B lines)
   - ESD protection placement (near connector)
   - Decoupling capacitor placement (near IC)
   - Ground plane integrity
3. Use 2-layer minimum (4-layer recommended for best performance)
4. Run DRC (Design Rule Check) before fabrication

### Step 3: Component Procurement

1. Use the BOM to order all components
2. Verify component availability and lead times
3. Order extras for prototyping (10-20% more)
4. Keep datasheets accessible during assembly

### Step 4: Assembly

1. Start with smallest components (resistors, capacitors)
2. Install IC (MAX3485) carefully with proper orientation
3. Install ESD protection diodes
4. Install connector last
5. Inspect solder joints under magnification
6. Clean flux residue if using flux-core solder

### Step 5: Testing and Validation

1. Perform visual inspection
2. Power-on test (measure VCC, verify current draw)
3. Electrical characterization (DC voltages, signal quality)
4. Protocol testing (NMEA sentence transmission/reception)
5. Interoperability testing with real NMEA devices
6. Follow complete compliance verification plan if needed

## Troubleshooting

### Common Issues and Solutions

**No Communication**
- Check power supply (3.3V present at transceiver?)
- Verify DE/RE control signals (toggling correctly?)
- Check cable connections (A and B not swapped?)
- Verify termination resistor (installed if at end of bus?)

**Poor Signal Quality**
- Check differential impedance (should be 100Ω)
- Verify ground plane continuity
- Check for proper termination (120Ω at both ends)
- Reduce cable length if excessive

**Intermittent Errors**
- Check ESD protection (diodes installed correctly?)
- Verify decoupling capacitors (close to IC?)
- Check for noise on power supply
- Verify baud rate accuracy (<1% error)

**ESD Damage**
- Ensure TVS diodes are properly installed
- Check ground connection integrity
- Verify proper handling procedures during assembly

## Design Iterations and Improvements

### Future Enhancements

Potential improvements for next revision:
- [ ] Add galvanic isolation (ISO1500 or ADM2582E)
- [ ] Implement automatic termination control
- [ ] Add surge protection (beyond ESD)
- [ ] Include bus fault protection
- [ ] Add diagnostic LEDs for power and faults
- [ ] Implement software-controlled baud rate selection

## Resources and References

### Datasheets

Key component datasheets should be stored in `datasheets/` directory:
- MAX3485.pdf - RS-485 transceiver datasheet
- PRTR5V0U2X.pdf - ESD protection diode datasheet
- Component datasheets for passive components

### Application Notes

Recommended reading:
1. **AN-960**: RS-485/RS-422 Circuit Implementation Guide (Analog Devices)
2. **SLLA414**: RS-485 PCB Layout Guidelines (Texas Instruments)
3. **MT-094**: High-Speed Layout Guidelines (Analog Devices)
4. **AN-1057**: Failsafe Biasing of RS-485 (Maxim)

### Standards Documents

- NMEA 0183 Standard Version 4.11
- TIA/EIA-422-B: Electrical Characteristics of Balanced Voltage Digital Interface Circuits
- TIA/EIA-485-A: Electrical Characteristics of Generators and Receivers
- IEC 61162-1: Maritime navigation equipment - Digital interfaces

### Online Resources

- [NMEA.org](https://www.nmea.org/) - Official NMEA organization
- [Maxim Integrated RS-485 Resources](https://www.maximintegrated.com/en/applications/interface/rs485.html)
- [Texas Instruments RS-485 Design Guide](https://www.ti.com/interface/rs-485/overview.html)

## Support and Contact

For technical questions or design support:
- Open an issue in the repository
- Review existing issues and discussions
- Check the documentation thoroughly before asking

## License

This hardware design is open source. Please refer to the repository license file for terms and conditions.

## Version History

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2025-10-21 | Initial RS-422/RS-485 interface design |

---

**Note**: This design is provided as-is for educational and development purposes. Always validate the design for your specific application requirements and perform proper testing before deployment in production systems.
