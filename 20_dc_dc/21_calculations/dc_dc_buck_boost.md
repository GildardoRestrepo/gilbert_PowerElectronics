---
title: "Buck-Boost Converter Design"
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
# Buck-Boost Converter Design

> [!NOTE]
> Calculation report for the inverting DC-DC Buck-Boost converter: 5 V input to −12 V output, assuming ideal semiconductors and continuous conduction mode (CCM).

## 1. Design premises

| Parameter                          | Value                      |
| ---------------------------------- | -------------------------- |
| Input voltage ($V_S$)              | $5\,\mathrm{V}$            |
| Output voltage ($V_o$)             | $-12\,\mathrm{V}$          |
| Output power ($P_o$)               | $6\,\mathrm{W}$ (assumed)  |
| Switching frequency ($f_{sw}$)     | $100\,\mathrm{kHz}$        |
| Output voltage ripple ($\Delta V_o/V_o$) | $2\,\%$              |
| Inductor current ripple target ($\Delta i_L/I_L$) | $30$–$40\,\%$ |

> [!IMPORTANT]
> Output power is an assumption ($I_o = 0.5\,\mathrm{A}$), to be confirmed against the lab's 5 V supply. Isolation: HCPL-2211, a 5 MBd logic-gate optocoupler (150 ns typical propagation delay, totem-pole output, 4.5–20 V supply). It is not a gate driver, so it drives the MOSFET through a level shifter or a small gate driver. At 100 kHz the ESP32 PWM keeps 800 counts per period (≈9.6 bits).

---
## 2. Duty cycle and load

$$
D = \frac{|V_o|}{V_S + |V_o|} = \frac{12\,\mathrm{V}}{5\,\mathrm{V} + 12\,\mathrm{V}} \approx 0.706
$$

$$
R = \frac{V_o^2}{P_o} = \frac{(12\,\mathrm{V})^2}{6\,\mathrm{W}} = 24\,\Omega
\qquad
I_o = 0.5\,\mathrm{A}
\qquad
I_L = \frac{I_o}{1-D} = 1.7\,\mathrm{A}
$$

The average inductor current equals the sum of input current ($1.2\,\mathrm{A}$) and output current ($0.5\,\mathrm{A}$).

---
## 3. Inductance

Critical inductance (CCM boundary):

$$
L_c = \frac{(1-D)^2 R}{2f_{sw}} = \frac{(0.294)^2(24\,\Omega)}{2(100\,\mathrm{kHz})} \approx 10.4\,\mu\mathrm{H}
$$

Design value from the inductor current ripple:

$$
\Delta i_L = \frac{V_S D}{L f_{sw}}
\quad\Rightarrow\quad
L = \frac{V_S D}{f_{sw}\,\Delta i_L}
$$

For a 30–40 % ripple ($\Delta i_L = 0.51$–$0.68\,\mathrm{A}$), $L = 52$–$69\,\mu\mathrm{H}$.

**Selected value: $L = 68\,\mu\mathrm{H}$**, which gives:

$$
\Delta i_L = \frac{(5\,\mathrm{V})(0.706)}{(68\,\mu\mathrm{H})(100\,\mathrm{kHz})} \approx 0.52\,\mathrm{A}\ (31\,\%)
\qquad
I_{L,max} \approx 1.96\,\mathrm{A}
\qquad
I_{L,min} \approx 1.44\,\mathrm{A}
$$

---
## 4. Capacitance

The minimum capacitance for a 2 % output voltage ripple is:

$$
C_{min} = \frac{D}{R f_{sw}\,(\Delta V_o/V_o)} = \frac{0.706}{(24\,\Omega)(100\,\mathrm{kHz})(0.02)} \approx 14.7\,\mu\mathrm{F}
$$

At this voltage level the ESR dominates the ripple. Its contribution is $I_{L,max}\cdot ESR$, so for a $0.24\,\mathrm{V}$ ripple (2 %):

$$
ESR < \frac{0.24\,\mathrm{V}}{1.96\,\mathrm{A}} \approx 0.12\,\Omega
$$

**Selected value: $C = 47\,\mu\mathrm{F}$, low-ESR polymer or electrolytic ($ESR \leq 0.1\,\Omega$), rated $\geq 25\,\mathrm{V}$**, which gives a capacitive ripple of:

$$
\frac{\Delta V_o}{V_o} = \frac{D}{R C f_{sw}} \approx 0.6\,\%
$$

The capacitor RMS current ($\approx 0.78\,\mathrm{A}$) must be within its ripple-current rating.

---
## 5. Component stresses

| Component | Max. voltage | Average current | RMS current | Peak current |
| --------- | ------------ | --------------- | ----------- | ------------ |
| Switch (MOSFET) | $V_S + \lvert V_o \rvert = 17\,\mathrm{V}$ → 30–40 V device | $1.2\,\mathrm{A}$ | $1.43\,\mathrm{A}$ | $1.96\,\mathrm{A}$ |
| Diode (Schottky) | $17\,\mathrm{V}$ reverse → 40 V device | $0.5\,\mathrm{A}$ | $0.93\,\mathrm{A}$ | $1.96\,\mathrm{A}$ |
| Inductor | — | $1.7\,\mathrm{A}$ | $1.71\,\mathrm{A}$ | $1.96\,\mathrm{A}$ (saturation, with margin) |
| Output capacitor | $12\,\mathrm{V}$ | — | $0.78\,\mathrm{A}$ | — |

The switch is high-side (between $V_S$ and the inductor), so its source is not at ground. At 5 V, a logic-level P-channel MOSFET driven by a small level shifter is the simplest option.

---
## 6. Ideal-model limitations

- At 5 V input, the forward drop of the Schottky diode (≈0.4 V) and the MOSFET conduction drop are no longer negligible. The real duty cycle will be higher than 0.706, and efficiency noticeably lower than 100 %.
- The PSIM model with real device parameters should be used to adjust $D$ before testing.

---
## 7. Selected components

| Component | Value | Rating |
| --------- | ----- | ------ |
| Inductor $L$ | $68\,\mu\mathrm{H}$ | $I_{sat} \geq 2.5\,\mathrm{A}$ |
| Output capacitor $C$ | $47\,\mu\mathrm{F}$ low-ESR | $\geq 25\,\mathrm{V}$, $ESR \leq 0.1\,\Omega$, $I_{rms} \geq 1\,\mathrm{A}$ |
| Switch | Logic-level P-MOSFET | $\geq 30\,\mathrm{V}$, $\geq 5\,\mathrm{A}$ |
| Diode | Schottky | $\geq 40\,\mathrm{V}$, $\geq 3\,\mathrm{A}$ |
| Isolation | HCPL-2211 + level shifter / gate driver | 5 MBd |

---
## 8. Purchase links

| Component | Link |
| --------- | ---- |
| Inductor $L$ | |
| Output capacitor $C$ | |
| Switch | |
| Diode | |
| Isolation | |

---
## 9. References

1. D. W. Hart, *Power Electronics*, McGraw-Hill, 2011, ch. 6 (buck-boost converter).
2. Broadcom, [HCPL-2211 datasheet (excerpt)](https://www.radiolocman.com/datasheet/data.html?di=603431).
3. Espressif, [ESP32 LEDC documentation](https://docs.espressif.com/projects/esp-idf/en/stable/api-reference/peripherals/ledc.html).

---
## Links
- [Buck converter design](dc_dc_buck.md)
- [Boost converter design](dc_dc_boost.md)
- [Project README](../../README.md)
