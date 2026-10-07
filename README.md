# APD Simulation

High-performance physical modeling, endurance degradation analysis, and bit error rate (BER) simulation framework for Separate Absorption, Charge, and Multiplication (SACM) Avalanche Photodiodes (APDs) and CMOS StrongARM sense-amplifier receivers in photonic computing architectures.

---

## Architecture Overview

This repository isolates the detector and receiver physics validation suite for ultra-low optical energy detection:
- **SACM APD Physics Engine**: Rigorous field-dependent ionization coefficients, dead-space history effects, McIntyre excess noise modeling, temperature-dependent breakdown voltage ($V_{br}$), and transit-time dynamics.
- **StrongARM Latch Engine**: Dynamic comparator model resolving regenerative sensing delay, input-referred offset voltage distributions, thermal and flicker noise integration, and clock-to-Q timing.
- **Endurance & Degradation Solver**: Hot-carrier injection (HCI), dielectric breakdown, dark count rate drift, and responsivity degradation across continuous operational cycles.
- **100M Monte Carlo BER Simulator**: High-throughput statistical verification achieving rigorous Bit Error Rate evaluation ($BER < 10^{-12}$) under optical power penalties and clock jitter.

---

## Repository Structure

```
APD Simulation/
├── apd_physics_engine.py         # SACM APD field, ionization, and transit dynamics
├── apd_endurance_solver.py       # Trap state generation, dark current drift, and lifetime
├── strongarm_latch_engine.py     # Sense amplifier regeneration, latch delay, and noise
├── run_100m_ber_simulation.py    # 100-million symbol Monte Carlo BER validation
├── test_apd_receiver.py          # Unit and regression verification suite
├── figures/                      # Simulation generated response curves and eye diagrams
├── .gitignore                    # Python build, cache, and artifact exclusions
└── README.md                     # Repository documentation
```

---

## Prerequisites

- **Python**: 3.10+ (tested on Python 3.11 and 3.12)
- **Dependencies**:
  ```bash
  pip install numpy scipy matplotlib
  ```

---

## Execution Guide

### 1. Run Unit Tests & Receiver Verification
Execute the test harness to verify ionization integrals, breakdown convergence, and regenerative latch timings:
```bash
python test_apd_receiver.py
```

### 2. Run 100M Monte Carlo BER Simulation
Execute the full 100-million symbol BER simulation under calibrated optical pulse trains:
```bash
python run_100m_ber_simulation.py
```

### 3. Run APD Physics & Endurance Solvers
Evaluate device I-V curves, excess noise factor $F(M)$, and long-term hot-carrier degradation:
```bash
python apd_physics_engine.py
python apd_endurance_solver.py
```

---

## License

MIT License. See individual files for copyright and licensing details.
