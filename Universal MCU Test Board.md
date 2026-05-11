# Universal Microcontroller Unit Test Board

A universal hardware fixture for running software-defined unit test suites against bare microcontrollers. The DUT (Device Under Test) is plugged into the board; a connected computer defines and runs the test suite with no manual wiring changes between different MCU models.

---

## The Gap

This does not exist as a unified product today. The landscape splits into five islands, none of which cover the full concept:

- **Commercial ATE** (Teradyne, NI PXI, dSPACE) — fully software-defined stimulus/response, but costs $50k–$3M and targets semiconductor fabs, not developer workbenches.
- **IC testers** (Arduino Smart IC Tester, Web-Based Digital IC Tester, Arduino IC Tester by Mechazawa) — correct form factor (ZIF socket + GPIO stimulus from host), but designed for legacy 74xx/4xxx logic chips, not programmable MCUs with JTAG, ADC, timers, or firmware.
- **Embedded test frameworks** (labgrid, Zephyr Twister, pytest-embedded, Unity/Ceedling, Jumpstarter, TinyHCI) — good software orchestration; require custom wiring per board and do not provide a universal hardware fixture.
- **ChipWhisperer CW308 UFO** — the closest hardware analog: a universal baseboard with interchangeable MCU target boards, fully scripted from Python. Purpose-built for side-channel security research, not functional unit testing.
- **JTAG/SWD probes** (OpenOCD, J-Link) — allow flashing and memory-level control of any JTAG-capable MCU, but do not provide pin stimulus/response at the application level.
- See also [[Trinkets]] for related hardware experiments.

---

## Next

- Explore whether anything closer to the concept has emerged in the open hardware / CI testing community.
- Consider what the minimal useful form factor would look like for a first prototype.
