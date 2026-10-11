# Portable Power Electronics Converters

Portable, enclosed power converters for teaching, sized to the supplies and loads of the Electric Machines Laboratory at Universidad Pontificia Bolivariana (UPB). Every unit follows the same path: calculation, simulation, PCB, build and lab validation. Each design can be reviewed and reproduced end to end.

> [!WARNING]
> These converters operate at up to 300 V DC and at mains-level AC. Build and test them only under lab supervision, with proper isolation and protection.

---
## Converters

| Family | Converter                        | Specification                                          | Status        |
| ------ | -------------------------------- | ------------------------------------------------------ | ------------- |
| AC/DC  | Three-phase full-wave rectifier  | 6-pulse                                                | Planned       |
| AC/DC  | Three-phase full-wave rectifier  | 12-pulse                                               | Planned       |
| AC/DC  | Three-phase full-wave rectifier  | 24-pulse (requires the lab's zigzag transformer)       | Planned       |
| DC/DC  | Buck                             | 300 V → 110 V                                          | Ideal PSIM model |
| DC/DC  | Boost                            | 110 V → 300 V                                          | Ideal PSIM model |
| DC/DC  | Buck-Boost (inverting)           | 5 V → −12 V                                            | Ideal PSIM model |
| DC/AC  | Three-phase full-wave inverter   | 110 or 220 V<sub>rms</sub> L-L (TBD)                   | Planned       |
| Control | External gating unit            | ESP32-based PWM / square-wave generator, optically isolated | Concept  |

---
## Repository structure

The repository is organized by converter family. Every family uses the same five stages, and each stage is numbered after its family (e.g. `2x` for DC/DC).

```
├── 10_ac_dc/                 AC/DC rectifiers
│   ├── 11_calculations/      Design calculations and component sizing
│   ├── 12_psim_simulation/   PSIM models
│   ├── 13_pcb_design/        KiCad schematics and PCB layouts
│   ├── 14_build/             Assembly notes, BOM and enclosure
│   └── 15_lab_test/          Test procedures and measured results
├── 20_dc_dc/                 DC/DC converters (same stages: 21–25)
├── 30_dc_ac/                 DC/AC inverters  (same stages: 31–35)
├── _docs/
│   ├── datasheets/           Component datasheets
│   └── style.md              Documentation style guide
├── LICENSE
└── README.md
```

---
## Workflow

1. **Calculations**: requirements, operating point and component sizing.
2. **Simulation**: validation in PSIM, first with ideal and then with real device models.
3. **PCB design**: schematic and layout in KiCad.
4. **Build**: assembly and 3D-printed enclosure.
5. **Lab test**: staged power-up and comparison against simulation.

---
## Tools

| Purpose            | Tool                  |
| ------------------ | --------------------- |
| Simulation         | PSIM                  |
| Schematic and PCB  | KiCad                 |
| Enclosure design   | Fusion 360            |
| Switching control  | ESP32 + Arduino IDE   |

---
## Documentation

All Markdown documents follow the [style guide](_docs/style.md).

---
## License

Released under the [MIT License](LICENSE).
