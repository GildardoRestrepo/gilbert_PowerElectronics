---
title: Buck Converter Design
created: 2026-08-22
time: 20:27
creator: Gilbert
last update: 2026-10-10
update by: Gilbert
type: calculation
status: draft
stage: 1
area: Power Electronics
editor: Gilbert
order: 0
tags:
  - type/calculation
---
# Buck Converter Design

> [!NOTE]
> Calculation report for the DC-DC Buck converter: 300 V input to 110 V output at 110 W, assuming ideal semiconductors and continuous conduction mode (CCM).

## 1. Design premises

| Parameter                          | Value                      |
| ---------------------------------- | -------------------------- |
| Input voltage ($V_S$)              | $300\,\mathrm{V}$          |
| Output voltage ($V_o$)             | $110\,\mathrm{V}$          |
| Output power ($P_o$)               | $110\,\mathrm{W}$          |
| Switching frequency ($f_{sw}$)     | $50\,\mathrm{kHz}$         |
| Output voltage ripple ($\Delta V_o/V_o$) | $2\,\%$              |
| Inductor current ripple target ($\Delta i_L/I_L$) | $30$–$40\,\%$ |

> [!IMPORTANT]
> Gate drive: HCPL-3120 (2.5 A peak output, 0.5 µs max. propagation delay, 15–30 V supply). At 50 kHz the worst-case delay is 2.5 % of the switching period, and the ESP32 PWM keeps 1600 counts per period (≈10.6 bits). A 600 V MOSFET is preferred over an IGBT at this frequency.

---
## 2. Duty cycle and load

$$
D = \frac{V_o}{V_S} = \frac{110\,\mathrm{V}}{300\,\mathrm{V}} \approx 0.367
$$

$$
R = \frac{V_o^2}{P_o} = \frac{(110\,\mathrm{V})^2}{110\,\mathrm{W}} = 110\,\Omega
\qquad
I_o = \frac{V_o}{R} = 1\,\mathrm{A}
$$

---
## 3. Inductance

The critical inductance is the minimum value that keeps the converter in CCM:

$$
L_c = \frac{(1-D)R}{2f_{sw}} = \frac{(1-0.367)(110\,\Omega)}{2(50\,\mathrm{kHz})} \approx 0.70\,\mathrm{mH}
$$

$L_c$ only guarantees CCM. The design value is chosen from the inductor current ripple:

$$
\Delta i_L = \frac{(V_S - V_o)D}{L f_{sw}}
\quad\Rightarrow\quad
L = \frac{(V_S - V_o)D}{f_{sw}\,\Delta i_L}
$$

For a 30–40 % ripple ($\Delta i_L = 0.3$–$0.4\,\mathrm{A}$), $L = 3.5$–$4.6\,\mathrm{mH}$.

**Selected value: $L = 4.7\,\mathrm{mH}$**, which gives:

$$
\Delta i_L = \frac{(190\,\mathrm{V})(0.367)}{(4.7\,\mathrm{mH})(50\,\mathrm{kHz})} \approx 0.30\,\mathrm{A}\ (30\,\%)
\qquad
I_{L,max} \approx 1.15\,\mathrm{A}
\qquad
I_{L,min} \approx 0.85\,\mathrm{A}
$$

---
## 4. Capacitance

The minimum capacitance for a 2 % output voltage ripple is:

$$
C_{min} = \frac{1-D}{8 L f_{sw}^2 \,(\Delta V_o/V_o)} = \frac{1-0.367}{8(4.7\,\mathrm{mH})(50\,\mathrm{kHz})^2(0.02)} \approx 0.34\,\mu\mathrm{F}
$$

**Selected value: $C = 1\,\mu\mathrm{F}$, film, rated $\geq 250\,\mathrm{V}$**, which gives:

$$
\frac{\Delta V_o}{V_o} = \frac{1-D}{8 L C f_{sw}^2} \approx 0.7\,\%
$$

The voltage rating covers the start-up overshoot described in section 6.

---
## 5. Component stresses

| Component | Max. voltage | Average current | RMS current | Peak current |
| --------- | ------------ | --------------- | ----------- | ------------ |
| Switch (MOSFET) | $300\,\mathrm{V}$ → 600 V device | $0.37\,\mathrm{A}$ | $0.61\,\mathrm{A}$ | $1.15\,\mathrm{A}$ |
| Diode (fast/ultrafast) | $300\,\mathrm{V}$ reverse → 600 V device | $0.63\,\mathrm{A}$ | $0.80\,\mathrm{A}$ | $1.15\,\mathrm{A}$ |
| Inductor | — | $1\,\mathrm{A}$ | $1.0\,\mathrm{A}$ | $1.15\,\mathrm{A}$ (saturation, with margin) |
| Output capacitor | $110\,\mathrm{V}$ (+ overshoot) | — | $0.09\,\mathrm{A}$ | — |

---
## 6. Start-up and safety notes

- The output LC filter has $f_0 = 1/(2\pi\sqrt{LC}) \approx 2.3\,\mathrm{kHz}$ and $Z_0 = \sqrt{L/C} \approx 69\,\Omega$. With $R = 110\,\Omega$, $Q \approx 1.6$ ($\zeta \approx 0.31$).
- A duty-cycle step therefore produces about **36 % overshoot (≈150 V)**, approaching $2V_o \approx 220\,\mathrm{V}$ at no load.
- The firmware must ramp $D$ (soft start). A cold lamp bank has a much lower resistance than at rated power, which increases the start-up current.

---
## 7. Selected components

| Component | Value | Rating |
| --------- | ----- | ------ |
| Inductor $L$ | $4.7\,\mathrm{mH}$ | $I_{sat} \geq 1.5\,\mathrm{A}$ |
| Output capacitor $C$ | $1\,\mu\mathrm{F}$ film | $\geq 250\,\mathrm{V}$ |
| Switch | MOSFET | $600\,\mathrm{V}$, $\geq 3\,\mathrm{A}$ |
| Diode | Ultrafast recovery | $600\,\mathrm{V}$, $\geq 3\,\mathrm{A}$ |
| Gate driver | HCPL-3120 | Isolated 15 V supply (high-side switch) |

---
## 8. Purchase links

| Component            | Link                                                                                                                                              |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Inductor $L$         | https://www.didacticaselectronicas.com/shop/bob-ch-10mh-2a-inductor-de-potencia-10mh-2a-46975                                                     |
| Output capacitor $C$ | https://www.didacticaselectronicas.com/shop/cp2-2uf250v-capacitor-de-poliester-2-2uf-250v-12277?attribute_values=1811%2C1840%2C1864               |
| Switch               | https://www.didacticaselectronicas.com/shop/gt50jr22-transistor-gt50jr22-canal-n-600v-50a-16330?attribute_values=1214%2C1396%2C1187%2C1394%2C1363 |
| Diode                | https://www.didacticaselectronicas.com/shop/fr607-diodo-ultra-rapido-1000v-6a-1-3v-6a-500ns-47324                                                 |

---
## 9. References

1. D. W. Hart, *Power Electronics*, McGraw-Hill, 2011, ch. 6 (buck converter).
2. Broadcom, [HCPL-3120 datasheet](https://docs.broadcom.com/doc/AV02-0161EN).
3. Espressif, [ESP32 LEDC documentation](https://docs.espressif.com/projects/esp-idf/en/stable/api-reference/peripherals/ledc.html).

---
## Links
- [Boost converter design](dc_dc_boost.md)
- [Buck-Boost converter design](dc_dc_buck_boost.md)
- [Project README](../../README.md)
