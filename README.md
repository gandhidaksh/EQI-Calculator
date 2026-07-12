[README.md](https://github.com/user-attachments/files/29934686/README.md)
# Effluent Quality Index (EQI) Calculator

A single-page tool that grades sewage treatment plant (STP) performance by scoring the
**inlet** (raw sewage) and **outlet** (treated effluent) on a common 0–500 index, and showing
how far the water improved through treatment.

**Live:** https://gandhidaksh.github.io/EQI-Calculator/

---

## What it does

Enter seven quality parameters at the inlet and outlet. The tool then:

1. Converts each raw reading into a **0–500 sub-index** using the NGT / CPCB band table
2. Combines the seven sub-indices into an overall **EQI** for each sampling point
3. Reports the **EQI improvement** across treatment, and the **change in each parameter**

Parameters: **BOD, COD, TSS, TN, TP, FC, pH**

## EQI scale

| Score | Category | Basis |
|---|---|---|
| 0–50 | Good | meets NGT norms |
| 51–100 | Satisfactory | meets CPCB norms |
| 101–200 | Moderate | |
| 201–300 | Poor | |
| 301–400 | Very Poor | |
| 401–500 | Severe | |

Lower is better. A parameter **complies** when its sub-index is ≤ 100.

## How the score is computed

Most parameters are placed in their quality band and **linearly interpolated**:

```
index = index_low + (reading − limit_low) / (limit_high − limit_low) × (index_high − index_low)
```

**Two parameters are handled differently:**

- **FC — logarithmic.** Faecal coliform spans 1 to 100,000 MPN/100 mL, so a linear fit inside
  a band distorts the score. FC is interpolated in log space:
  ```
  index = index_low + (index_high − index_low) × (log C − log C_low) / (log C_high − log C_low)
  ```
- **pH — two-sided.** pH can be bad in either direction. Readings ≥ 7.0 are graded on the
  alkaline bands; readings < 7.0 on the acidic bands, where interpolation runs in reverse
  (a lower pH is worse). pH change is reported as a shift toward neutral, not a removal %.

**Combining the sub-indices:** if every parameter complies, the EQI is the equal-weighted mean
of all seven. If any parameter fails, the EQI is the mean of *only the failing* parameters — so
a single serious breach cannot be masked by good numbers elsewhere.

## Validation

The engine reproduces the reference Excel exactly. Clicking **Load Excel reference** loads the
source sample (BOD 25, COD 224, TSS 459, TN 17, TP 7, FC 70, pH 7.75), which yields
**EQI 363 — Very Poor**, matching the reference sheet's `EQI_wmean`.

> Note: the reference Excel computes FC linearly (70 → 35). This calculator follows the thesis
> equation and computes FC logarithmically (70 → 46). This does not affect the 363 result,
> because FC complies in that sample and does not enter the failing-parameter mean.

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

## Credits

- **EQI methodology formulated by** Dipansh Jain
- **Calculator developed by** Daksh Gandhi
- Under the guidance of **Prof Sovik Das** and **Dr Anil**
- Department of Civil & Environmental Engineering, **IIT Delhi**

Feedback: dassovik@iitd.ac.in
