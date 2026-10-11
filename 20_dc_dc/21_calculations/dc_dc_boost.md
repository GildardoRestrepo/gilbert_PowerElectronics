---
title: "Boost Converter Design"
created: 2026-10-10
time: ""
creator: "Gilbert"
last update: 2026-10-10
update by: "Gilbert"
type: calculation
status: draft
stage: 1
area: power
editor: "Gilbert"
order: 0
tags:
  - type/calculation
---
# Boost Converter Design

> [!NOTE]
> Calculation report for the DC-DC Boost converter: 110 V input to 300 V output at 110 W, assuming ideal semiconductors and continuous conduction mode (CCM).

## 1. Design premises

| Parameter                          | Value                      |
| ---------------------------------- | -------------------------- |
| Input voltage ($V_S$)              | $110\,\mathrm{V}$          |
| Output voltage ($V_o$)             | $300\,\mathrm{V}$          |
| Output power ($P_o$)               | $110\,\mathrm{W}$          |
| Switching frequency ($f_{sw}$)     | $50\,\mathrm{kHz}$         |
| Output voltage ripple ($\Delta V_o/V_o$) | $2\,\%$              |
| Inductor current ripple target ($\Delta i_L/I_L$) | $30$–$40\,\%$ |

> [!NOTE]
> Power and switching frequency match the Buck converter, so both converters share the same inductor current (1 A) and can use the same inductor. Gate drive: HCPL-3120 (2.5 A peak output, 0.5 µs max. propagation delay).

---
## 2. Duty cycle and load

$$
D = 1 - \frac{V_S}{V_o} = 1 - \frac{110\,\mathrm{V}}{300\,\mathrm{V}} \approx 0.633
$$

$$
R = \frac{V_o^2}{P_o} = \frac{(300\,\mathrm{V})^2}{110\,\mathrm{W}} \approx 818\,\Omega
\qquad
I_o = \frac{V_o}{R} \approx 0.367\,\mathrm{A}
\qquad
I_L = \frac{I_o}{1-D} = 1\,\mathrm{A}
$$

---
## 3. Inductance

Critical inductance (CCM boundary):

$$
L_c = \frac{D(1-D)^2 R}{2f_{sw}} = \frac{(0.633)(0.367)^2(818\,\Omega)}{2(50\,\mathrm{kHz})} \approx 0.70\,\mathrm{mH}
$$

Design value from the inductor current ripple:

$$
\Delta i_L = \frac{V_S D}{L f_{sw}}
\quad\Rightarrow\quad
L = \frac{V_S D}{f_{sw}\,\Delta i_L}
$$

For a 30–40 % ripple ($\Delta i_L = 0.3$–$0.4\,\mathrm{A}$), $L = 3.5$–$4.6\,\mathrm{mH}$.

**Selected value: $L = 4.7\,\mathrm{mH}$**, which gives:

$$
\Delta i_L = \frac{(110\,\mathrm{V})(0.633)}{(4.7\,\mathrm{mH})(50\,\mathrm{kHz})} \approx 0.30\,\mathrm{A}\ (30\,\%)
\qquad
I_{L,max} \approx 1.15\,\mathrm{A}
\qquad
I_{L,min} \approx 0.85\,\mathrm{A}
$$

---
## 4. Capacitance

The minimum capacitance for a 2 % output voltage ripple is:

$$
C_{min} = \frac{D}{R f_{sw}\,(\Delta V_o/V_o)} = \frac{0.633}{(818\,\Omega)(50\,\mathrm{kHz})(0.02)} \approx 0.77\,\mu\mathrm{F}
$$

**Selected value: $C = 2.2\,\mu\mathrm{F}$, film, rated $\geq 450\,\mathrm{V}$**, which gives:

$$
\frac{\Delta V_o}{V_o} = \frac{D}{R C f_{sw}} \approx 0.7\,\%
$$

The output capacitor carries the pulsed diode current, so its RMS current rating ($\approx 0.49\,\mathrm{A}$) must be checked in the datasheet. The ESR contribution to the ripple is $I_{L,max}\cdot ESR$; for it to stay below $6\,\mathrm{V}$ (2 %), $ESR < 5\,\Omega$, which any film capacitor meets.

---
## 5. Component stresses

| Component | Max. voltage | Average current | RMS current | Peak current |
| --------- | ------------ | --------------- | ----------- | ------------ |
| Switch (MOSFET) | $300\,\mathrm{V}$ → 600 V device | $0.63\,\mathrm{A}$ | $0.80\,\mathrm{A}$ | $1.15\,\mathrm{A}$ |
| Diode (fast/ultrafast) | $300\,\mathrm{V}$ reverse → 600 V device | $0.37\,\mathrm{A}$ | $0.61\,\mathrm{A}$ | $1.15\,\mathrm{A}$ |
| Inductor | — | $1\,\mathrm{A}$ | $1.0\,\mathrm{A}$ | $1.15\,\mathrm{A}$ (saturation, with margin) |
| Output capacitor | $300\,\mathrm{V}$ (+ overshoot) | — | $0.49\,\mathrm{A}$ | — |

The switch is low-side (source at ground), so its gate driver needs no floating supply.

---
## 6. Start-up and safety notes

> [!WARNING]
> In open loop the output voltage rises without limit as the load decreases, because the ideal conversion ratio $V_o/V_S = 1/(1-D)$ only holds in CCM. **Never operate the Boost without load**, and always ramp $D$ from zero (soft start).

- When the input is connected, the output capacitor charges to $V_S$ through $L$ and the diode, even with the switch off. This LC transient can overshoot up to about $2V_S \approx 220\,\mathrm{V}$, so add input inrush limiting (NTC or pre-charge resistor).
- The $450\,\mathrm{V}$ capacitor rating and 600 V semiconductors give margin for these transients. An overvoltage shutdown (comparator on $V_o$ wired to the gating unit's fault input) is recommended.

---
## 7. Selected components

| Component | Value | Rating |
| --------- | ----- | ------ |
| Inductor $L$ | $4.7\,\mathrm{mH}$ (same as Buck) | $I_{sat} \geq 1.5\,\mathrm{A}$ |
| Output capacitor $C$ | $2.2\,\mu\mathrm{F}$ film | $\geq 450\,\mathrm{V}$, $I_{rms} \geq 0.5\,\mathrm{A}$ |
| Switch | MOSFET | $600\,\mathrm{V}$, $\geq 3\,\mathrm{A}$ |
| Diode | Ultrafast recovery | $600\,\mathrm{V}$, $\geq 3\,\mathrm{A}$ |
| Gate driver | HCPL-3120 | 15 V supply referenced to ground (low-side switch) |

---
## 8. Purchase links

| Component | Link |
| --------- | ---- |
| Inductor $L$ | |
| Output capacitor $C$ | |
| Switch | |
| Diode | |
| Gate driver | |

---
## 9. References

1. D. W. Hart, *Power Electronics*, McGraw-Hill, 2011, ch. 6 (boost converter).
2. Broadcom, [HCPL-3120 datasheet](https://docs.broadcom.com/doc/AV02-0161EN).

---
## Links
- [Buck converter design](dc_dc_buck.md)
- [Buck-Boost converter design](dc_dc_buck_boost.md)
- [Project README](../../README.md)
