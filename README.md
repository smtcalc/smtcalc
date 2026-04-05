# SMTCalc

Free, browser-based tools for electronics manufacturing professionals.
https://smtcalc.com

---

## What It Is

SMTCalc is a collection of practical calculators and reference tools for SMT process
engineers, operators, and quality inspectors. Every tool runs entirely in the browser.
No account, no data collection, no ads, no monetization.

The site started as a personal toolbox. Having reliable, standards-based calculators
available without hunting through spreadsheets or vendor datasheets. It grew from there.

---

## Tools

| Tool | Description |
|------|-------------|
| Stencil Architect | Aperture area ratio, transfer efficiency, stencil thickness guidance per IPC-7525A |
| SMT Capacity Calculator | Theoretical vs. actual line throughput |
| SMT Line Capacity Planner | Multi-line shift planning and utilization |
| MSL Bake Calculator | Moisture sensitive device bake time per J-STD-033D |
| MSL Tracker | Excel-based floor life tracking tool (download) |
| Reflow Throughput Calculator | Board-per-hour output based on conveyor speed and board spacing |
| Placement Speed Verifier | Verify machine CPH claims against real board and program data |
| Bare Board Bake Calculator | Pre-assembly bake requirements per IPC-1601 |
| Resistor Code Calculator | 4-band and 5-band resistor color code decoding |
| Solder Alloy Reference | Alloy compositions, melting ranges, and application notes |
| Flux Designator Decoder | IPC J-STD-004 flux designator breakdown |
| IPC Standards Finder | Index of IPC and J-STD standards with descriptions |

---

## Philosophy

Information should be free.

The electronics manufacturing industry has no shortage of people who treat their knowledge
as a competitive asset. That culture holds the industry back. The tools on this site come
from real work on real production floors, derived from IPC standards and hands-on experience.
Making them freely available is not charity. It is just the right thing to do.

---

## About the Author

The author has been working in electronics manufacturing since 1997, with close to 30 years of
hands-on experience in SMT process engineering, IPC standards compliance, quality inspection,
and PCB cleaning and coating processes. He holds current GEA certifications in Manufacturing
Engineering for Electronics Assembly and has maintained IPC-A-610 and J-STD-001 certifications.

---

## Technical Notes

- Static site hosted on GitHub Pages. No frameworks, no build tools, no backend.
- Each tool lives in its own subdirectory with its own index.html.
- Shared styles in /style.css. No external CSS dependencies beyond Google Fonts (loaded via @import).
- All calculations run client-side in plain JavaScript.
- HTML and JS are 7-bit ASCII-clean throughout.

---

## License

Code released under the MIT License -- free to use, modify, and adapt.

See [LICENSE](LICENSE) for the full text.
