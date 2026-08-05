# Effluent Quality Index (EQI) Calculator

A single-page tool that grades sewage treatment plant (STP) performance by scoring the
**inlet** (raw sewage) and **outlet** (treated effluent) on a common 0–500 index, and showing
the extent till which the sewage undergoes treatment.

**Live:** https://gandhidaksh.github.io/EQI-Calculator/

---

## What it does

Enter seven quality parameters at the inlet and outlet. The tool then:

1. Converts each raw reading into a **0–500 sub-index** using the NGT / CPCB band table
2. Combines the seven sub-indices into an overall **EQI** for each sampling point
3. Reports the **EQI improvement** across treatment, and the **change in each parameter**

Parameters: **BOD, COD, TSS, TN, TP, FC, pH**

## EQI scale

| Score range | Category | Compliance |
|---|---|---|
| 0–50 | Good | Linked to [NGT standards for sewage discharge](https://images.assettype.com/barandbench/import/2019/01/NS-Deshpande-v-UOI-NGT-order-Dec-21-2018.pdf) |
| 51–100 | Satisfactory | Linked to [CPCB standards](https://cpcb.nic.in/GeneralStandards.pdf) |
| 101–200 | Moderate | Non compliant with discharge standards |
| 201–300 | Poor | Non compliant with discharge standards |
| 301–400 | Very Poor | Non compliant with discharge standards |
| 401–500 | Severe | Non compliant with discharge standards |
| >500 | Severe+ | Non compliant with discharge standards |

Lower is better. A parameter **complies** when its sub-index is ≤ 100.

## How the score is computed

Each raw concentration reading is placed in its quality band and interpolated in its corresponding score range to obtain the SI score in the defined index. The
formula used depends on the parameter:

```
Eq 1:  SI = L + (U − L) × (C − C_L) / (C_U − C_L)
Eq 2:  SI = L + (U − L) × (C_U − C) / (C_U − C_L)
Eq 3:  SI = L + (U − L) × (log C − log C_L) / (log C_U − log C_L)
```

Where `SI` = sub-index score · `C` = measured concentration · `C_L`, `C_U` = lower and upper
bounds of its band · `L`, `U` = lower and upper score bounds of that band.

- **Eq 1**: BOD, COD, TSS, TN, TP, the alkaline side of pH, and FC within the Good band
  (1–100 MPN/100 mL).
- **Eq 2**: the acidic side of pH. pH can be bad in either direction; below 7.0 the
  interpolation runs in reverse, since a lower pH is worse. Its change is reported as a shift
  toward neutral, not a removal %.
- **Eq 3**: FC above the Good band. Faecal coliform spans several orders of magnitude, so each
  concentration term is log-transformed before interpolating. For the same reason FC reduction
  is reported as **log removal**, log₁₀(inlet ÷ outlet), not a percentage.

**Combining the sub-indices:**

- If **every parameter complies**, the overall EQI is the **maximum** of the seven sub-indices —
  the plant is graded on its worst-performing parameter.
- If **any parameter fails**, the overall EQI is the **weighted mean of the non-compliant
  parameters only** (so a serious breach cannot be diluted by good numbers elsewhere. The
  failing parameters are listed in brackets after the score, in decreasing order of sub-index —
  e.g. `363 (TP, TSS)`), so the reported figure always shows where it came from.

## Validation

The engine reproduces the reference Excel exactly. Clicking **Load Excel reference** loads the
source sample and yields **EQI 363 (Very Poor)**, matching the reference sheet's `EQI_wmean`.

| Parameter | Reading | Sub-index | Sheet |
|---|---|---|---|
| BOD | 25 mg/L | 87 | 87 |
| COD | 224 mg/L | 94 | 94 |
| TSS | 459 mg/L | 344 | 344 |
| TN | 17 mg/L | 85 | 85 |
| TP | 7 mg/L | 382 | 382 |
| FC | 70 MPN/100mL | 35 | 35 |
| pH | 7.75 | 66 | 66 |

## Running it

No installation, no server, no dependencies. It is a single self-contained HTML file.

- **Online:** open the live link above
- **Locally:** download `index.html` and double-click it

## Deployment

Hosted on GitHub Pages from the `main` branch, root folder. Any commit to `index.html`
goes live automatically within a minute.

## Roadmap

Built as a calculator, structured to extend into a dashboard once year-wise geospatial STP data
is available — the same static approach can carry map and time-series views without a backend.

## References

- MoEFCC (1986). *The Environment (Protection) Rules, 1986 — Schedule VI: General Standards for
  Discharge of Environmental Pollutants (Effluents).* https://cpcb.nic.in/GeneralStandards.pdf
- *Nitin Shankar Deshpande v. Union of India & Others* (2018). NGT order, 21 December 2018.
  https://images.assettype.com/barandbench/import/2019/01/NS-Deshpande-v-UOI-NGT-order-Dec-21-2018.pdf

## Credits

- **EQI methodology formulated by** Dipansh Jain
- **Calculator developed by** Daksh Gandhi
- Under the guidance of **Prof Sovik Das** and **Dr Anil**
- Department of Civil & Environmental Engineering, **IIT Delhi**

Feedback: dassovik@iitd.ac.in
