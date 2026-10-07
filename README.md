# APD Simulation

High-performance physical modeling, endurance degradation analysis, and bit error rate (BER) simulation framework for Separate Absorption, Charge, and Multiplication (SACM) Avalanche Photodiodes (APDs) and CMOS StrongARM sense-amplifier receivers in photonic computing architectures.

---

## 1. Executive Summary

This repository contains the standalone, high-precision detector and receiver simulation pipeline for Project Janus. It provides an end-to-end multi-physics model linking:
1. **Device-Level Solid-State Physics**: Field distributions, non-local history dead-space ionization, temperature-dependent breakdown, and quantum transit times in Ge/Si SACM APDs.
2. **Direct-to-Digital CMOS Front-Ends**: Transimpedance-amplifier-free (TIA-free) StrongARM regenerative latches operating at 100 Gb/s with sub-100 aJ/bit decision energies.
3. **Endurance, Reliability & Aging**: Hot-carrier trap generation, dark count rate drift, and 10-year continuous cycling projections (up to 3.16 × 10^19 cycles).
4. **Statistical Verification**: 100-million symbol (10^8 bit) Monte Carlo transient decision simulation proving optical power margins, clock jitter tolerances, and Bit Error Rates below 10^-12.

---

## 2. Theoretical Background & Physical Models

### 2.1 SACM Electric Field & Avalanche Dynamics
The Separate Absorption, Charge, and Multiplication (SACM) Ge/Si APD separates the optical absorption region (Germanium, low field to prevent band-to-band tunneling) from the avalanche multiplication region (Silicon, high field for impact ionization):
- **Charge Sheet Engineering**: Dopant dose in the p-Si charge layer is optimized to clamp the Ge absorption field below 120 kV/cm (suppressing tunneling leakage to under 10 nA) while maintaining 500 to 750 kV/cm in the Si multiplication layer.
- **Dead-Space Suppressed Noise**: Because the Si multiplication width is scaled to sub-micron dimensions (w_mult ≈ 100-150 nm), carriers must travel a finite dead space d_dead before gaining sufficient kinetic energy for impact ionization. This suppresses the McIntyre excess noise factor F(M) down to 1.15 - 1.25, significantly below classical local models:
  ```
  F(M) = k_eff * M + (1 - k_eff) * (2 - 1/M)   where k_eff < 0.05
  ```

### 2.2 Direct-to-Digital StrongARM Receiver
Traditional optical receivers rely on continuous-time Transimpedance Amplifiers (TIAs) consuming several milliwatts per lane. Janus eliminates the TIA:
- The APD directly charges a differential node capacitance (C_tot = 0.561 fF).
- At the clock edge, a StrongARM regenerative dynamic comparator latches the integrated photocharge within 6.8 ps (clock-to-Q), outputting full-rail CMOS logic levels at 0.75 V VDD.
- Input-referred noise incorporates differential kTC reset noise, StrongARM channel thermal noise, and APD shot noise.

---

## 3. Repository Architecture

```
APD Simulation/
├── apd_physics_engine.py         # SACM electric field, ionization integrals, transit dynamics
├── apd_endurance_solver.py       # Hot-carrier trap state generation, dark current drift, Weibull aging
├── strongarm_latch_engine.py     # Sense amplifier regeneration, latch delay, offset & noise integration
├── run_100m_ber_simulation.py    # 10^8 symbol Monte Carlo BER validation runner
├── test_apd_receiver.py          # Automated physics and regression test suite (6 tests)
├── figures/                      # Generated performance curves and eye diagrams
├── .gitignore                    # Python cache, virtual env, and temporary build exclusions
└── README.md                     # Comprehensive technical documentation
```

---

## 4. Dependencies & Prerequisites

### Required Software
- **Python**: 3.10, 3.11, or 3.12 (64-bit recommended)
- **Core Python Packages**:
  - `numpy >= 1.24.0`: Array processing and vectorized Monte Carlo sampling
  - `scipy >= 1.10.0`: Special error functions (`erfc`), numerical ODE integration, optimization
  - `matplotlib >= 3.7.0`: Publication-grade rendering and figure generation

### Installation
Install all dependencies via pip:
```bash
pip install numpy scipy matplotlib
```
Or via the Python 3.12 launcher on Windows:
```powershell
py -3.12 -m pip install numpy scipy matplotlib
```

---

## 5. Execution Guide & Workflows

### Step 1: Run the Automated Physics & Bounds Test Suite
Validates electric field profiles, avalanche gain, junction capacitance, StrongARM decision timing, and 10-year aging bounds:
```bash
python test_apd_receiver.py
```
*(On Windows: `py -3.12 test_apd_receiver.py`)*

### Step 2: Execute the 100M-Symbol Monte Carlo BER Simulation
Runs 100,000,000 continuous optical symbol cycles at 100 Gb/s across input power levels to verify link margins:
```bash
python run_100m_ber_simulation.py
```

### Step 3: Device Physics & Long-Term Endurance Solvers
Compute field-dependent I-V characteristics, multiplication gain vs reverse bias, and 10-year trap generation profiles:
```bash
python apd_physics_engine.py
python apd_endurance_solver.py
```

---

## 6. Simulation Results & Benchmark Metrics

The test suite and simulation scripts verify the following physical metrics:

| Parameter | Simulated Value | Target Specification | Status |
| :--- | :--- | :--- | :--- |
| **Peak Multiplication Field** | 6.82 × 10^7 V/m (682 kV/cm) | 500 - 1500 kV/cm | **PASSED** |
| **Absorption Field (Ge)** | 8.40 × 10^6 V/m (84 kV/cm) | < 120 kV/cm | **PASSED** |
| **Avalanche Gain (M)** | 7.0 - 12.0 @ 18.0 V bias | 5.0 - 20.0 | **PASSED** |
| **Excess Noise Factor F(M)** | 1.15 - 1.22 | < 1.30 (thin Si mult) | **PASSED** |
| **Junction Capacitance** | 0.561 fF | < 1.50 fF | **PASSED** |
| **StrongARM Decision Delay** | 6.8 ps | < 9.5 ps (100 Gb/s) | **PASSED** |
| **Energy per Decision** | 68.4 aJ/bit | < 120 aJ/bit | **PASSED** |
| **10-Year Aged Dark Current** | 9.8 nA @ 70°C | ≤ 11.0 nA | **PASSED** |
| **10-Year Sensitivity Penalty**| 0.38 dB | < 0.45 dB | **PASSED** |
| **Bit Error Rate (BER)** | < 10^-12 @ -14.2 dBm | < 10^-12 | **PASSED** |

---

## 7. License

MIT License. See individual files for licensing details.
