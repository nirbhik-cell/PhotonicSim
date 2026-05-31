# REPOSITORY_STRUCTURE.md

# PhotonicSim Repository Structure

This document defines the official repository layout for PhotonicSim.

The goal is to maintain a clean separation between:

* Physics
* Numerical Methods
* Solvers
* Experiments
* Documentation
* Validation

The repository structure should remain stable throughout the project's lifetime.

---

# Top-Level Structure

```text
PhotonicSim/
│
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CHANGELOG.md
├── pyproject.toml
├── requirements.txt
│
├── docs/
├── photonicSim/
├── tests/
├── examples/
├── benchmarks/
├── future/
└── assets/
```

---

# Documentation

```text
docs/
│
├── REPOSITORY_STRUCTURE.md
├── VISION.md
├── ARCHITECTURE.md
├── ROADMAP.md
├── API.md
│
├── theory/
├── tutorials/
├── examples/
└── developer/
```

Purpose:

* Project vision
* Architecture specification
* Tutorials
* Theoretical references
* Developer documentation

---

# Main Package

```text
photonicSim/
│
├── __init__.py
│
├── core/
├── physics/
├── materials/
├── geometry/
├── coordinates/
├── meshes/
├── domains/
├── sources/
├── monitors/
├── detectors/
├── components/
├── solvers/
├── nonlinear/
├── lasers/
├── quantities/
├── numerics/
├── visualization/
├── validation/
├── benchmarks/
├── io/
├── plugins/
├── experiments/
└── simulation/
```

---

# Core

Contains framework-wide infrastructure.

```text
core/
│
├── units.py
├── constants.py
├── backend.py
├── registry.py
├── config.py
├── metadata.py
├── state_machine.py
├── logging.py
└── exceptions.py
```

Responsibilities:

* Units
* Physical constants
* Backend abstraction
* Plugin registration
* Metadata tracking
* Simulation lifecycle

---

# Physics

Contains physical models independent of numerical solvers.

```text
physics/
│
├── dispersion/
├── susceptibilities/
├── responses/
├── phase_matching/
├── noise/
├── conservation/
├── observables/
├── thermo_optics/
└── loss_models/
```

---

# Materials

Contains material definitions and databases.

```text
materials/
│
├── base.py
├── loaders.py
│
└── databases/
    ├── silica.yaml
    ├── silicon.yaml
    ├── air.yaml
    ├── gold.yaml
    └── silver.yaml
```

---

# Geometry

Pure geometric primitives.

```text
geometry/
│
├── rectangle.py
├── circle.py
├── polygon.py
├── cylinder.py
├── sphere.py
└── prism.py
```

Geometry must remain solver-independent.

---

# Coordinates

Coordinate systems.

```text
coordinates/
│
├── cartesian.py
├── cylindrical.py
└── spherical.py
```

---

# Meshes

Numerical meshes.

```text
meshes/
│
├── uniform.py
├── yee.py
├── adaptive.py
├── fem.py
└── spectral.py
```

Only required mesh types should be implemented.

---

# Domains

Physical representations.

```text
domains/
│
├── base.py
├── ray_domain.py
├── field_domain.py
├── envelope_domain.py
├── spectral_domain.py
└── modal_domain.py
```

---

# Sources

Optical sources.

```text
sources/
│
├── base.py
├── cw_laser.py
├── pulsed_laser.py
├── gaussian_beam.py
├── plane_wave.py
├── dipole.py
└── custom.py
```

---

# Monitors

Simulation probes.

```text
monitors/
│
├── field_monitor.py
├── flux_monitor.py
├── mode_monitor.py
├── spectrum_monitor.py
└── energy_monitor.py
```

---

# Detectors

Physical measurement devices.

```text
detectors/
│
├── power_meter.py
├── spectrometer.py
├── camera.py
└── photodiode.py
```

---

# Components

Reusable optical elements.

```text
components/
│
├── lens.py
├── mirror.py
├── aperture.py
├── fiber.py
├── waveguide.py
├── grating.py
└── resonator.py
```

---

# Solvers

Numerical simulation engines.

```text
solvers/
│
├── base.py
│
├── fourier/
├── ssfm/
├── gnlse/
├── modes/
├── bpm/
├── fdtd/
├── rcwa/
└── fem/
```

Not all solvers are required for v1.

---

# Nonlinear

Nonlinear optical interactions.

```text
nonlinear/
│
├── interactions/
│   ├── spm.py
│   ├── xpm.py
│   ├── fwm.py
│   ├── raman.py
│   ├── mi.py
│   └── dispersive_wave.py
│
├── diagnostics/
├── propagation/
├── gnlse/
└── supercontinuum/
```

---

# Lasers

Laser physics models.

```text
lasers/
│
├── gain_media.py
├── rate_equations.py
├── fabry_perot.py
├── ring_laser.py
├── q_switching.py
└── mode_locking.py
```

---

# Quantities

Strongly typed physical quantities.

```text
quantities/
│
├── power.py
├── attenuation.py
├── dispersion.py
├── effective_index.py
├── nonlinear_coefficient.py
└── group_velocity.py
```

---

# Numerics

Reusable numerical tools.

```text
numerics/
│
├── fft/
├── ode/
├── pde/
├── linear_algebra/
├── interpolation/
└── root_finding/
```

---

# Visualization

Plotting and animations.

```text
visualization/
│
├── plotting.py
├── animation.py
├── spectra.py
├── fields.py
└── modes.py
```

---

# Validation

Validation framework.

```text
validation/
│
├── theory/
├── references/
├── metrics/
└── reports/
```

---

# Benchmarks

Performance and accuracy benchmarks.

```text
benchmarks/
│
├── gaussian_beam/
├── fiber_modes/
├── fwm/
└── supercontinuum/
```

---

# IO

Data import/export.

```text
io/
│
├── hdf5.py
├── zarr.py
├── yaml.py
└── json.py
```

---

# Plugins

Third-party extensions.

```text
plugins/
│
└── registry.py
```

---

# Experiments

High-level workflows.

```text
experiments/
│
├── dispersion_analysis.py
├── fiber_transmission.py
├── fwm_experiment.py
├── supercontinuum_experiment.py
└── gaussian_beam_experiment.py
```

---

# Simulation

Simulation orchestration.

```text
simulation/
│
├── simulation.py
├── result.py
├── context.py
└── response_graph.py
```

---

# Tests

Mirrors package structure.

```text
tests/
│
├── core/
├── physics/
├── materials/
├── solvers/
├── nonlinear/
├── experiments/
└── validation/
```

---

# Examples

User-facing examples.

```text
examples/
│
├── gaussian_beam.py
├── dispersion_analysis.py
├── fiber_modes.py
├── fwm.py
├── ssfm.py
└── supercontinuum.py
```

---

# Future

Modules intentionally excluded from v1.

```text
future/
│
├── circuit_photonics/
├── quantum_photonics/
├── inverse_design/
├── rcwa_expansion/
└── fem_expansion/
```

---

# Repository Philosophy

1. Physics and numerics remain separated.
2. Solvers remain interchangeable.
3. Fiber optics and nonlinear photonics are first-class citizens.
4. Validation is mandatory.
5. Future modules must extend, not redesign, the architecture.
6. Every major feature must have tests, documentation, and examples.

```
```
