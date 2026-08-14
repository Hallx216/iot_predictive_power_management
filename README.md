# Predictive Power Management in IoT Devices
## Using Low-Complexity Machine Learning and Dynamic Scheduling

**Student:** Rishabh Yadav | A00048105
**Supervisor:** Dr Jennifer McManis
**Institution:** Dublin City University — School of Electronic Engineering
**Module:** EEN1095
**Date:** August 2026

---

## Project Overview

This project investigates whether low-complexity machine learning can resolve the fundamental tension in IoT power management: reducing energy consumption requires spending more time in low-power sleep states, yet every minute asleep risks missing an occupancy event the system was deployed to detect.

A Logistic Regression classifier is trained on environmental sensor data from the UCI Occupancy Detection dataset to estimate the probability of next-step room occupancy. That probability drives a three-state hardware scheduler — Active, Light Sleep, or Deep Sleep — on the STM32L476 microcontroller. A realistic Wake–Read–Predict–Sleep (WRPS) operational loop explicitly models the data-continuity problem that arises when a sleeping device wakes with stale sensor readings, using gap-aware delta normalisation to correct for it.

**Key results at recommended thresholds (P_high = 0.5, P_low = 0.4):**

| Metric | Value |
|---|---|
| Test accuracy | 0.978 |
| Test AUC-ROC | 0.995 |
| Energy saving vs always-active | 80.0% |
| Missed-activity rate (WRPS simulation) | 2.2% (median) |
| Battery life — always-active baseline | 12.5 days |
| Battery life — ML scheduler | 62.4 days |
| Wilcoxon p-value vs duty-cycle baseline | p = 0.0078 |

---

## Research Question

Can low-complexity machine learning combined with dynamic scheduling increase the energy efficiency of IoT sensor nodes relative to static duty-cycling, while maintaining an acceptable missed-activity rate?

---

## Repository Structure

```
iot_predictive_power_management/
├── README.md                                         ← This file
├── requirements.txt                                  ← Python dependencies
├── notebooks/
│   ├── 01_baseline_predictive_model.ipynb            ← Feature engineering + LR model
│   ├── 02_model_comparison.ipynb                     ← LR vs Decision Tree vs Random Forest
│   ├── gap01_wake_read_predict_sleep_loop.ipynb      ← WRPS loop + gap-aware normalisation
│   ├── 03_energy_model.ipynb                         ← STM32L476 energy model + battery life
│   ├── gap02_duty_cycle_baseline.ipynb               ← Fixed-period duty-cycle comparison
│   ├── gap03_cross_validation_significance.ipynb     ← 5-fold CV + Wilcoxon signed-rank test
│   ├── 04_threshold_sensitivity_analysis.ipynb       ← 12-configuration threshold sweep
│   └── 05_final_results_summary.ipynb                ← Consolidated results + master figure
├── data/
│   ├── datatraining.txt                              ← UCI training set (8,144 obs.)
│   ├── datatest.txt                                  ← UCI test set 1 (2,665 obs.)
│   ├── datatest2.txt                                 ← UCI test set 2 — primary eval (9,752 obs.)
│   └── final_results_summary.csv                     ← Canonical results export (Notebook 05)
└── figures/
    ├── threshold_sensitivity_sweep.png               ← Paper Figure 2
    ├── duty_cycle_pareto.png                         ← Paper Figure 3
    ├── wilcoxon_paired_comparison.png                ← Appendix E, Fig. E.1
    ├── energy_model_summary.png                      ← Appendix E, Fig. E.4
    ├── master_results_summary.png                    ← Appendix E, Fig. E.5
    ├── wrps_timeline_comparison.png                  ← Appendix E, Fig. E.2
    └── wrps_energy_vs_miss_rate.png                  ← Appendix E, Fig. E.3
```

---

## Dataset

**UCI Occupancy Detection Dataset**
Candanedo, L. M. and Feldheim, V. (2016). *Accurate occupancy detection of an office room from light, temperature, humidity and CO2 measurements using statistical learning models.* Energy and Buildings, vol. 112, pp. 28–39.
https://archive.ics.uci.edu/dataset/357/occupancy+detection

The three dataset files (datatraining.txt, datatest.txt, datatest2.txt) are included in the `data/` directory. All notebooks read them using relative paths (`../data/`) and can be run without downloading the dataset separately.

---

## How to Run the Notebooks

### Step 1 — Clone or download the repository

```bash
git clone https://github.com/Hallx216/iot_predictive_power_management.git
cd iot_predictive_power_management
```

### Step 2 — Install dependencies

```bash
pip install -r requirements.txt
```

Python 3.8 or later is required.

### Step 3 — Launch Jupyter

```bash
jupyter notebook
```

Open the browser window that appears and navigate to the `notebooks/` folder.

### Step 4 — Run a notebook

Open any notebook and select **Kernel → Restart & Run All** to execute all cells on a clean kernel. Each notebook is self-contained — it re-trains the model from the raw data files and does not require any other notebook to be run first.

### Recommended execution order (for full pipeline)

```
01 → 02 → gap01 → 03 → gap02 → gap03 → 04 → 05
```

### Validating a notebook on a clean kernel

```bash
jupyter execute --inplace notebooks/05_final_results_summary.ipynb
```

---

## Notebook Summary

| Notebook | Purpose | Key output |
|---|---|---|
| 01 — Baseline model | Load UCI data, engineer 16 features, train LR | Accuracy 0.978, AUC 0.995 |
| 02 — Model comparison | Compare LR, Decision Tree, Random Forest | Justifies LR selection (0.86 KB, 146 µs) |
| gap01 — WRPS loop | Simulate WRPS with gap-aware normalisation | Timeline figure, energy/miss-rate comparison |
| 03 — Energy model | STM32L476 power values, battery-life projection | 81.2% saving, 12.5 → 66.6 days (provisional) |
| gap02 — Duty cycle | Sweep 11 duty-cycle periods, Pareto plot | Baseline trade-off curve for paper Figure 3 |
| gap03 — CV + Wilcoxon | 5-fold CV, Wilcoxon signed-rank test | CV 0.990 ± 0.001, W=28, p=0.0078 |
| 04 — Threshold sweep | 12 (P_high, P_low) configurations | Recommended pair: (0.5, 0.4) |
| 05 — Final results | Consolidated results at recommended thresholds | 80.0% saving, 2.2% miss rate, CSV export |

---

## Hardware Target

**STM32L476** — Ultra-low-power Arm Cortex-M4 32-bit MCU

Power values used in the energy model (from datasheet DS10198 Rev 9, 2024):

| State | Power draw |
|---|---|
| Active (run mode) | 10 mW |
| Light Sleep (sleep mode) | 1 mW |
| Deep Sleep (Stop 2 mode, RTC only) | 0.01 mW |

---

## Tools and Libraries

| Package | Purpose |
|---|---|
| scikit-learn | Logistic Regression, cross-validation, metrics |
| pandas | Data loading and manipulation |
| numpy | Numerical computation |
| matplotlib | Figure generation |
| scipy | Wilcoxon signed-rank test |
| jupyter / nbclient | Notebook execution and validation |

Install all with: `pip install -r requirements.txt`

---

## Common Issues

**FileNotFoundError on data files**
Ensure you are running notebooks from inside the `notebooks/` directory, or that the `data/` folder exists at the same level as `notebooks/`. The expected structure is `../data/datatraining.txt` relative to the notebook.

**Figures not appearing inline**
The notebooks use `matplotlib.use('Agg')` which saves figures to `figures/` rather than displaying them inline. This is intentional so notebooks commit cleanly. Open the `figures/` folder to view all generated plots.

**plt.show() produces nothing**
Same reason as above — the Agg backend saves to file, not screen. The figure is in `figures/` as a PNG.

---

## Project Status

All notebooks complete and executed. IEEE conference paper submitted. Portfolio submitted to Loop (August 2026).
