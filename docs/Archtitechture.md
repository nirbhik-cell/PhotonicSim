# PhotonicSim Architecture Specification

Version: 1.0

Status: Frozen Architecture

---

# Purpose

This document defines the architectural principles, core abstractions, module relationships, and design constraints governing PhotonicSim.

All future development must comply with the principles described here.

The goal is to ensure:

* Consistency
* Extensibility
* Scientific correctness
* Long-term maintainability

throughout the lifetime of the project.

---

# Architectural Principles

---

## Principle 1: Physics Is Independent Of Numerics

Physical models must never contain solver-specific code.

Forbidden:

```python id="x9w4jf"
class KerrMaterial:
    def fdtd_update(self):
        pass
```

Allowed:

```text id="cvywb8"
Material
 ↓
Adapter
 ↓
Solver
```

Materials describe physics.

Solvers describe numerical implementation.

---

## Principle 2: Everything Has Units

All physical quantities must carry units.

Forbidden:

```python id="gw6knr"
Laser(wavelength=1550)
```

Allowed:

```python id="h0gk48"
Laser(wavelength=1550*nm)
```

Internally all quantities must be converted to SI units.

---

## Principle 3: Single Source Of Truth

A physical quantity must have exactly one canonical representation.

Example:

Store:

ω

Derive:

* λ
* f
* k

Never store multiple equivalent representations simultaneously.

---

## Principle 4: Solvers Are Replaceable

Any compatible physical system should be solvable by multiple solvers.

Example:

```text id="1h7b6g"
Material
Geometry
Field
```

should be usable with:

* SSFM
* BPM
* FDTD
* FEM
* RCWA

without modification.

---

## Principle 5: Validation Is Mandatory

Every major feature requires:

* Theory
* Reference solution
* Validation test
* Benchmark

before being considered complete.

---

# High-Level Architecture

```text id="r6mvlz"
Physics Layer
      ↓
Abstraction Layer
      ↓
Numerical Layer
      ↓
Backend Layer
```

---

## Physics Layer

Contains:

* Materials
* Responses
* Susceptibilities
* Phase Matching
* Conservation Laws
* Noise Models

This layer contains no numerical implementation details.

---

## Abstraction Layer

Provides common representations.

Examples:

* Domains
* Geometry
* Quantities
* Sources
* Components

This layer connects physics to numerical methods.

---

## Numerical Layer

Contains:

* Solvers
* Numerical Utilities
* Meshes

Responsible for computation.

---

## Backend Layer

Provides computational resources.

Current:

* NumPy
* SciPy

Future:

* CuPy
* PyTorch
* JAX

---

# Core Module Responsibilities

---

# Core

Provides framework infrastructure.

Responsibilities:

* Units
* Constants
* Configuration
* Registry
* Backend Management
* Metadata
* Logging
* Exceptions

All other modules depend on Core.

---

# Physics

Provides physical models.

Responsibilities:

* Dispersion
* Nonlinearity
* Material Responses
* Noise
* Conservation
* Phase Matching

Physics modules must remain solver-independent.

---

# Materials

Provides:

* Material Definitions
* Material Databases
* Material Loading

Materials are physical descriptions.

Materials are not numerical objects.

---

# Geometry

Provides:

* Shapes
* Transformations
* Spatial Definitions

Geometry must remain independent of:

* Solvers
* Meshes

---

# Coordinates

Provides:

* Cartesian Coordinates
* Cylindrical Coordinates
* Spherical Coordinates

Required by:

* Fibers
* Gaussian Beams
* Resonators

---

# Meshes

Provides discretization structures.

Supported mesh types:

* UniformGrid
* YeeGrid
* AdaptiveMesh

Future:

* FEMMesh
* SpectralMesh

Meshes belong to numerical methods, not physics.

---

# Domains

Domains define physical representations.

---

## Ray Domain

Represents:

* Position
* Direction
* Power

Used by:

* Ray Optics

---

## Field Domain

Represents:

* Ex
* Ey
* Ez
* Hx
* Hy
* Hz

Used by:

* FDTD
* Electromagnetic Solvers

---

## Envelope Domain

Represents:

A(z,t)

Used by:

* SSFM
* GNLSE
* BPM

---

## Spectral Domain

Represents:

A(ω)

Used by:

* FWM
* Raman
* Supercontinuum

---

## Modal Domain

Represents:

* Effective Index
* Propagation Constants
* Mode Profiles

Used by:

* Mode Solvers

---

# Sources

Provide energy injection.

Supported source types:

* CW Laser
* Pulsed Laser
* Plane Wave
* Gaussian Beam
* Dipole Source

Sources generate fields.

Sources do not propagate fields.

---

# Monitors

Provide simulation measurements.

Examples:

* FieldMonitor
* SpectrumMonitor
* FluxMonitor

Monitors are solver-side objects.

---

# Detectors

Represent experimental instruments.

Examples:

* Spectrometer
* Power Meter
* Photodiode

Detectors are physical abstractions.

---

# Components

Represent physical optical elements.

Examples:

* Fibers
* Waveguides
* Lenses
* Mirrors
* Resonators

Components combine:

* Geometry
* Materials
* Physical behavior

---

# Quantities

Provides strongly typed physical quantities.

Examples:

* Power
* Dispersion
* Attenuation
* Effective Index
* Nonlinear Coefficient

Benefits:

* Unit safety
* Metadata support
* Consistent plotting

---

# Numerics

Provides reusable numerical algorithms.

Includes:

* FFT
* ODE Solvers
* Linear Algebra
* Interpolation
* Root Finding

Numerics should remain solver-independent.

---

# Solvers

Provide numerical implementations.

All solvers must implement:

```python id="g1s29r"
build()
initialize()
solve()
postprocess()
export()
```

Required solver types:

* Fourier Solver
* SSFM Solver
* GNLSE Solver
* Mode Solver

Future:

* BPM
* FDTD
* RCWA
* FEM

---

# Nonlinear Architecture

Nonlinear optics is treated as a first-class subsystem.

---

## Interaction Engine

Represents energy exchange mechanisms.

Examples:

* SPM
* XPM
* FWM
* Raman
* MI

Interactions are physical processes.

Interactions are not solvers.

---

## Phase Matching Framework

Provides:

Δk calculations

Used by:

* SHG
* FWM
* OPO
* DFG

Must remain solver-independent.

---

## Noise Framework

Provides:

* Shot Noise
* ASE Noise
* Thermal Noise

Noise should be injectable into simulations.

---

## Conservation Framework

Provides automatic checks for:

* Energy
* Power
* Photon Number

Used during validation and debugging.

---

## Diagnostics Framework

Provides:

* Soliton Number
* Dispersion Length
* Nonlinear Length
* Conversion Efficiency
* Bandwidth
* Coherence

---

# Response Graph

Provides multiphysics coupling.

Example:

Field
→ Heating
→ Index Change
→ Field

Another example:

Field
→ Carrier Generation
→ Index Change
→ Field

ResponseGraph manages dependencies between responses.

---

# Simulation Architecture

---

## Simulation

Represents the orchestration layer.

Responsible for:

* Configuration
* Solver Selection
* Execution
* Result Collection

---

## SimulationResult

Common result container.

Contains:

* Fields
* Spectra
* Modes
* Power
* Metadata

All solvers must return SimulationResult.

---

# Storage Architecture

Supported formats:

* HDF5

Future:

* Zarr

Large datasets must support disk-backed storage.

---

# Plugin Architecture

All extensions must be registrable.

Examples:

```python id="nqqm2v"
register_solver()

register_material()

register_component()
```

Plugins must extend existing abstractions.

Plugins must not modify core modules.

---

# Future Architecture

The following areas are explicitly excluded from v1:

* Circuit Photonics
* Quantum Photonics
* RCWA Expansion
* FEM Expansion
* Inverse Design
* Adjoint Optimization

These should be implemented as future extensions.

---

# Architecture Freeze Policy

The following abstractions are considered foundational:

* Material
* Domain
* Source
* Component
* Solver
* Simulation
* SimulationResult
* Response
* Observable

Changes to these interfaces require architectural review.

All other systems should be implemented around these abstractions.

---

# Final Design Goal

PhotonicSim should evolve into a modular, scientifically rigorous, and extensible photonics framework capable of supporting education, research, and future development without requiring major architectural redesign.
