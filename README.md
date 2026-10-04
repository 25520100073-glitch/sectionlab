# SectionLab

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A verified open-source Python library for structural cross-section analysis.

---

## Installation

Install SectionLab in editable mode directly from source:

```bash
pip install -e .
```

To include development dependencies (testing and linting tools):

```bash
pip install -e ".[dev]"
```

## Quick Start

*(Coming soon — Quick start examples and API tutorials will be available here.)*

## Architecture

SectionLab is structured into three clean, decoupled layers:

1. **SBVL Core (`sectionlab.core`, `sectionlab.mechanics`)**:
   - Fundamental cross-section geometric properties (area, centroid, principal moments of inertia via Green's theorem and parallel axis theorem).
   - Classical strength of materials (SBVL): axial, bending, torsion stresses, shear stress distributions via Zhuravsky formula, and Mohr's stress circles.
   - Determinate beam analysis with continuous deflection curves (Clebsch initial parameters) and Vereshchagin/Simpson diagram multiplication.

2. **RC Section Core (`sectionlab.rc`, `sectionlab.codes`)**:
   - Nonlinear reinforced concrete cross-section mechanics using fiber section discretization.
   - Nonlinear material constitutive models (Whitney stress block, parabolic-rectangular concrete, elastoplastic steel).
   - Strain compatibility equilibrium solver via safeguarded Newton-Raphson.
   - P-M interaction curves and moment-curvature (\(M\)-\(\kappa\)) analysis.
   - Pluggable design codes architecture (e.g., TCVN 5574:2018, AS 3600:2018).

3. **Report & Output Layer (`sectionlab.report`, `sectionlab.cli`)**:
   - Step-by-step calculation trace and formula documentation.
   - Command-line interface (`sectionlab`) for running benchmarks and section queries.
   - Structured visual and tabular calculation exports.

## Unit Convention

SectionLab uses a strictly consistent unit system across all modules:

| Physical Quantity | Unit | Symbol |
| :--- | :--- | :--- |
| Force | Newton | \(\text{N}\) |
| Length / Dimensions | Millimeter | \(\text{mm}\) |
| Stress / Modulus of Elasticity | Megapascal | \(\text{MPa} = \text{N/mm}^2\) |
| Angle | Radian | \(\text{rad}\) |

*(Note: Moments are expressed in \(\text{N}\cdot\text{mm}\).)*

## Validation

*(Coming soon — Benchmark suite validation matrix comparing SectionLab outputs against classical analytical solutions and standard textbook benchmarks.)*

## Disclaimer

> **SectionLab is intended for educational use and cross-checking only. It does not replace the judgement of a licensed structural engineer.**
