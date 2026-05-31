# PhotonicSim Implementation Plan

## Development Order

Infrastructure
→ Physics
→ Domains
→ Solvers
→ Applications

Rule:

Never build an upper layer before the lower layer is stable.

---

# Phase 0 — Infrastructure

Target: v0.1

Duration: 1–2 weeks

## Repository Setup

Create:

PhotonicSim/
- README.md
- LICENSE
- .gitignore
- pyproject.toml
- requirements.txt

---

## Documentation

Create:

docs/
- REPOSITORY_STRUCTURE.md
- VISION.md
- ARCHITECTURE.md
- ROADMAP.md
- IMPLEMENTATION_PLAN.md
- API.md

---

## Core Package

Create:

photonicSim/core/

Files:

- units.py
- constants.py
- backend.py
- registry.py
- metadata.py
- exceptions.py
- state_machine.py

---

## Units System

Recommended: Pint

Required examples:

```python
1550 * nm
100 * fs
10 * mW
```

---

## Constants

Implement:

```python
c
eps0
mu0
h
hbar
e
```

---

## Backend

Never use NumPy directly outside backend.

Example:

```python
backend.array(...)
backend.fft(...)
backend.eig(...)
```

Backend v1:

- NumPy
- SciPy

Future:

- CuPy
- PyTorch
- JAX

---

## Metadata System

Every simulation stores:

```python
{
    "version": "...",
    "backend": "...",
    "solver": "...",
    "timestamp": "..."
}
```

---

## Testing

Install:

- pytest
- coverage
- ruff
- mypy

Create:

tests/

---

## Exit Criteria

- Units work
- Constants work
- Backend works
- Tests run

---

# Phase 1 — Physics Foundation

Target: v0.2

Duration: 2–4 weeks

## Material Base Class

Create:

```python
class Material:
    pass
```

Responsibilities:

- dispersion
- loss
- responses

Not responsible for:

- solvers
- meshes

Required API:

```python
material.index(...)
material.permittivity(...)
material.loss(...)
```

---

## Dispersion Models

Implement:

- Constant Index
- Sellmeier
- Lorentz
- Drude

Validation:

Compare against literature values.

---

## Response System

Base:

```python
class Response:
    pass
```

Derived:

- KerrResponse
- RamanResponse
- ThermalResponse

---

## Material Database

Folder:

materials/databases/

Format:

```yaml
name: silica

dispersion:
  model: sellmeier

responses:
  - kerr
```

---

## Exit Criteria

```python
silica = Silica()

silica.index(1550 * nm)
```

works.

---

# Phase 2 — Domains

Target: v0.3

Duration: 2–3 weeks

## Envelope Domain

Represents:

A(t)

and

A(z,t)

Used by:

- SSFM
- BPM
- GNLSE

---

## Spectral Domain

Represents:

A(ω)

Used by:

- FWM
- Raman
- Supercontinuum

---

## Modal Domain

Stores:

- neff
- beta
- mode profile

---

## Conversion Layer

Implement:

Envelope ↔ Spectral

using FFT.

Validation:

Transform Gaussian pulse.

Recover original pulse.

---

## Exit Criteria

Time-domain ↔ Frequency-domain conversion works accurately.

---

# Phase 3 — Fiber Foundation

Target: v0.4

Duration: 2–4 weeks

## Fiber Classes

Implement:

```python
SingleModeFiber
MultiModeFiber
PhotonicCrystalFiber
```

---

## Properties

Store:

- length
- gamma
- alpha
- effective_area
- dispersion

---

## Dispersion Engine

Compute:

- beta
- beta2
- beta3
- beta4

from refractive index data.

---

## Diagnostics

Implement:

```python
dispersion_length()

nonlinear_length()

soliton_number()
```

---

## Exit Criteria

Fiber characterization works.

---

# Phase 4 — Mode Solver

Target: v0.5

Duration: 3–5 weeks

## Solver

Implement:

Finite Difference Eigenmode Solver

Outputs:

- neff
- beta
- mode profile

---

## Validation

Compare with analytical LP modes.

---

## Exit Criteria

Fundamental fiber modes solved accurately.

---

# Phase 5 — SSFM

Target: v0.6

Duration: 4–6 weeks

Use:

Symmetric Split-Step Fourier Method

Not simple split-step.

---

## Components

Implement:

```python
LinearOperator

NonlinearOperator

SSFMSolver
```

---

## Validation

### Dispersion Only

Gaussian broadening

### Nonlinearity Only

SPM

### Soliton

Fundamental soliton propagation

---

## Exit Criteria

Research-quality pulse propagation.

---

# Phase 6 — Nonlinear Interaction Engine

Target: v0.7

## Base Class

```python
class Interaction:
    pass
```

Derived:

- SPMInteraction
- XPMInteraction
- FWMInteraction
- RamanInteraction

---

## FWM Engine

Avoid brute force.

Use:

- frequency indexing
- sparse interaction graph
- energy conservation pruning
- phase matching pruning

Outputs:

- efficiency
- gain spectrum
- allowed interactions

---

## Phase Matching

Implement:

Δβ calculations

Support:

- FWM
- SHG
- SFG
- DFG

---

## Exit Criteria

Broadband nonlinear interactions simulated.

---

# Phase 7 — Raman & Self-Steepening

Target: v0.8

Implement:

- Raman kernel
- delayed response
- self steepening

Add:

- shot noise
- ASE noise
- seed noise

---

## Exit Criteria

Realistic ultrafast propagation.

---

# Phase 8 — GNLSE

Target: v0.9

Combine:

- dispersion
- Kerr
- Raman
- self steepening
- loss
- noise

into a single propagation engine.

---

## Validation

Reproduce literature examples.

---

## Exit Criteria

Research-grade GNLSE solver.

---

# Phase 9 — Supercontinuum Framework

Target: v1.0

Create:

```python
SupercontinuumExperiment
```

Outputs:

- spectrum evolution
- temporal evolution
- coherence
- bandwidth
- energy conservation

---

## Diagnostics

Compute:

- LD
- LNL
- soliton number
- coherence
- conversion efficiency

---

## Exit Criteria

Publication-quality supercontinuum simulations.

---

# After v1.0

Implement:

- Wave optics
- Fourier optics
- Gaussian beam propagation
- 1D FDTD
- BPM
- 2D FDTD
- Laser physics

---

# Highest Priority Order

1. Units
2. Materials
3. Domains
4. Fibers
5. Mode Solver
6. SSFM
7. FWM
8. Raman
9. GNLSE
10. Supercontinuum

This sequence provides the fastest route to a useful nonlinear fiber optics research framework while preserving the long-term PhotonicSim architecture.