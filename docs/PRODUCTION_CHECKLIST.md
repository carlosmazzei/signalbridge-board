# Production Checklist for Raspberry Pi Pico Breakout Boards

This checklist is intended to help ensure that your Raspberry Pi Pico breakout board design is ready for production, whether you are assembling it yourself (DIY) or sending it to a contract manufacturer (PCBA). It covers key areas such as design verification, bill of materials (BOM) accuracy, assembly considerations, and test point inclusion.

## Additional PCBA (Assembly) Checklist

Use this section if the board will be assembled by a factory (turnkey or consigned).

### A) BOM correctness and availability

- [ ] Export a **clean BOM** (MPN + manufacturer + description + value + package + quantity).
- [ ] Ensure **every line** has a valid **Manufacturer Part Number (MPN)** (avoid “generic” unless intentionally flexible).
- [ ] Prefer **in-stock / active lifecycle** parts (avoid NRND/EOL when possible).
- [ ] For each critical component, provide at least one **approved alternate** (same footprint + same key specs).
- [ ] Verify **tolerance/voltage/power** ratings:
  - [ ] Resistors: power rating (e.g., 0.1W vs 0.25W), tolerance
  - [ ] Capacitors: voltage derating (X5R/X7R loss), temp range
  - [ ] Inductors (if any): saturation current, DCR
  - [ ] Diodes/TVS: standoff voltage, peak power
- [ ] Check **package availability** (0402/0201 can raise cost/yield risk; use 0603/0805 when acceptable).
- [ ] Confirm all passives have **standard values** (avoid exotic values unless needed).
- [ ] If using a specific assembler’s catalog (e.g., JLC/LCSC), verify:
  - [ ] Correct library part number (e.g., LCSC code) is included
  - [ ] “Basic/Preferred” vs “Extended” parts are intentional (cost/lead time impact)
  - [ ] Stock levels cover the required quantity + spares

### B) Footprints, polarity, and pick-and-place orientation

- [ ] Verify **pin 1** marking is clear on footprint and silkscreen for all ICs.
- [ ] Verify polarity for:
  - [ ] LEDs (anode/cathode)
  - [ ] Diodes/TVS
  - [ ] Electrolytics/tantalums
  - [ ] Any keyed connectors
- [ ] Confirm the footprint origin/centroid is correct (especially for connectors and large parts).
- [ ] Check **rotation conventions** (0/90/180/270) against the assembler’s expected orientation.
- [ ] Ensure all SMD parts are placed on the **correct side** (Top/Bottom) per assembly plan.
- [ ] If assembling both sides:
  - [ ] Confirm the assembler supports it and the process constraints are met
  - [ ] Avoid heavy parts on the second-reflow side unless allowed

### C) Placement rules (DFM/DFA)

- [ ] Check **component-to-component clearance** meets assembler minimums (including courtyards).
- [ ] Maintain a **keepout** around board edges as required by panel rails/handling (typ. 3–5 mm; follow assembler spec).
- [ ] Keep components away from:
  - [ ] Mounting holes (tool access + washers)
  - [ ] Fiducials
  - [ ] Panel breakaway tabs / mouse-bites / V-cuts
- [ ] Ensure adequate **soldering access** for any through-hole parts (if wave/selective/hand-solder is used).
- [ ] Use **thermal reliefs** for through-hole pads tied to planes (hand-solder friendliness).
- [ ] Review very small passives near connectors/edges (risk of tombstoning/damage).

### D) Solder paste and stencil considerations

- [ ] Review paste apertures for fine-pitch parts (if any) to avoid bridging.
- [ ] For large exposed pads (if any), consider paste windowing to prevent float/voiding.
- [ ] Avoid uneven copper/paste that can cause **tombstoning** (especially on 0402/0201).
- [ ] Confirm solder mask expansion avoids accidental mask-defined pads unless intentional.

### E) Fiducials, tooling holes, and panelization

- [ ] Add at least **3 global fiducials** on the assembled side (and on the opposite side if double-sided).
- [ ] Add **local fiducials** near fine-pitch ICs (if present).
- [ ] Include **tooling holes** if your assembler requires them (often 2–4).
- [ ] Confirm panelization approach:
  - [ ] Single board vs panel
  - [ ] V-score vs tabs/mouse-bites
  - [ ] Rails width and component keepout on rails

### F) Assembly outputs and documentation package

- [ ] Generate **Pick-and-Place / CPL** with correct:
  - [ ] RefDes
  - [ ] X/Y coordinates
  - [ ] Rotation
  - [ ] Side (Top/Bottom)
- [ ] Provide **Assembly drawings** (PDF) for Top/Bottom with:
  - [ ] RefDes
  - [ ] Polarity markers
  - [ ] Connector orientation notes
- [ ] Provide **Gerbers** + **drill files** as usual, plus:
  - [ ] Paste layers (`F.Paste`/`B.Paste`) if stencil is ordered
  - [ ] Fab notes (stack-up, finish, thickness, soldermask color if relevant)
- [ ] If connectors are not to be populated, clearly mark **DNP/DNI** parts in BOM and assembly notes.
- [ ] Include a **test plan** (even minimal):
  - [ ] Power rails check points (3V3, VSYS, GND)
  - [ ] UART header loopback test pads (optional)
  - [ ] I²C pull-up verification points
  - [ ] SPI continuity checks (SCK/MOSI/MISO/CS)

### G) Supply chain / risk checks (recommended)

- [ ] Identify single-source parts (connectors, regulators, crystals) and define alternates.
- [ ] Flag long lead-time parts and verify ordering strategy (turnkey vs consigned).
- [ ] Confirm RoHS/REACH requirements if needed.
- [ ] Confirm moisture sensitivity (MSL) handling requirements for any sensitive packages (rare in simple breakouts, but good practice).

---

## Test Points (PCBA + Bring-up) Checklist

Add/verify test points to make assembly test, debugging, and production bring-up reliable.

### A) Power test points (mandatory)

- [ ] Add clearly labeled test points for:
  - [ ] `GND` (at least 2, preferably near opposite sides of the board)
  - [ ] `3V3`
  - [ ] `VSYS` / `VBUS` / `5V` (whichever rails exist on the board)
- [ ] Place at least one `GND` test point close to each power rail test point (probe-friendly).
- [ ] Ensure power test points are reachable with probes after assembly (not blocked by tall connectors).

### B) Interface test points (recommended)

- [ ] SPI test points (if exposed via headers, still add near the Pico for debug):
  - [ ] `SPI_SCK`
  - [ ] `SPI_MOSI`
  - [ ] `SPI_MISO`
  - [ ] `SPI_CS` (or at least one representative CS)
- [ ] I²C test points:
  - [ ] `I2C_SDA`
  - [ ] `I2C_SCL`
- [ ] UART test points:
  - [ ] `UART_TX`
  - [ ] `UART_RX`
- [ ] Add at least one nearby `GND` test point for clean signal probing (especially for SPI SCK).

### C) Reset / boot / debug (if applicable)

- [ ] Add test points for key control signals as applicable:
  - [ ] `RUN` / `RESET` (Pico reset line)
  - [ ] `BOOTSEL` (if routed out)
- [ ] If SWD is available on the design, add a test point/header for:
  - [ ] `SWDIO`
  - [ ] `SWCLK`
  - [ ] `GND`
  - [ ] `3V3` (reference)

### D) Placement and probing rules

- [ ] Use consistent test point style:
  - [ ] Prefer dedicated test point footprints (round pad) over bare vias.
  - [ ] Minimum exposed pad diameter per assembler/test method (typ. ≥ 1.0 mm; follow assembler spec).
- [ ] Maintain adequate spacing between test points for probes (typ. ≥ 1.0–1.5 mm; follow probe type).
- [ ] Keep test points away from board edges/panel break tabs/rails keepouts.
- [ ] Ensure test points are on the intended side for test (usually Top side for pogo fixtures).
- [ ] Avoid placing test points under connectors, Pico module overhang, or other tall parts.

### E) Electrical correctness and labeling

- [ ] Confirm each test point is assigned to the correct **net** (no accidental net ties).
- [ ] Ensure silkscreen labels are readable:
  - [ ] Use short, unambiguous labels (`TP_GND`, `TP_3V3`, `TP_SCK`, etc.).
- [ ] If the design uses multiple grounds (e.g., `AGND/PGND`), ensure test points reflect the intended net (prefer a single solid GND unless there is a clear reason).

### F) Factory test support (optional but valuable)

- [ ] Add a dedicated `TP_GND` and `TP_3V3` pair near where a fixture would clamp/probe.
- [ ] Consider adding a simple production test header/pads for quick checks:
  - [ ] `GND`, `3V3`, `UART_TX`, `UART_RX` (minimum set)
- [ ] Document expected bring-up measurements in the assembly/test notes:
  - [ ] `3V3` nominal range
  - [ ] `VSYS/VBUS` nominal range (if present)
  - [ ] Idle levels for UART/I²C lines (pull-ups, etc.)
