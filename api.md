# PhotonicSim Public API Specification

Version: 1.0

Status: Frozen Core Interfaces

---

# Purpose

This document defines the public API of PhotonicSim.

Core interfaces defined here are considered stable and form the foundation of the framework.

All future modules should build upon these interfaces rather than replacing them.

---

# Design Goals

The API should be:

* Physics-first
* Solver-independent
* Extensible
* Consistent
* Unit-safe

---

# Core Objects

The following objects are considered foundational:

```text
Material
Response
Interaction
Domain
Source
Component
Solver
Simulation
SimulationResult
Observable
Experiment
```

Changes to these interfaces require architectural review.

---

# Material

Represents a physical material.

A material describes physical properties.

A material does NOT perform numerical calculations.

---

## Interface

```python
class Material:

    name: str

    def index(self, omega):
        ...

    def permittivity(self, omega):
        ...

    def permeability(self, omega):
        ...

    def loss(self, omega):
        ...

    def add_response(self, response):
        ...

    def responses(self):
        ...
```

---

## Examples

```python
silica = Silica()

silica.index(omega)

silica.permittivity(omega)
```

---

# Response

Represents a material response.

Examples:

* Kerr
* Raman
* Thermal
* Carrier
* Pockels

---

## Interface

```python
class Response:

    name: str

    def evaluate(
        self,
        field,
        material,
        state
    ):
        ...
```

---

## Examples

```python
KerrResponse()

RamanResponse()

ThermalResponse()
```

---

# Interaction

Represents energy exchange processes.

Examples:

* SPM
* XPM
* FWM
* Raman
* Modulation Instability

---

## Interface

```python
class Interaction:

    name: str

    def evaluate(
        self,
        domain,
        material,
        state
    ):
        ...
```

---

## Examples

```python
SPMInteraction()

FWMInteraction()

RamanInteraction()
```

---

# Domain

Represents a physical representation of light.

All solvers operate on domains.

---

## Interface

```python
class Domain:

    name: str

    def copy(self):
        ...

    def normalize(self):
        ...

    def metadata(self):
        ...
```

---

# EnvelopeDomain

Represents:

A(z,t)

---

## Interface

```python
class EnvelopeDomain(Domain):

    time

    amplitude

    power()

    energy()
```

---

# SpectralDomain

Represents:

A(ω)

---

## Interface

```python
class SpectralDomain(Domain):

    omega

    spectrum

    bandwidth()

    center_frequency()
```

---

# ModalDomain

Represents:

* Effective index
* Propagation constants
* Mode profiles

---

## Interface

```python
class ModalDomain(Domain):

    neff

    beta

    mode_profile
```

---

# FieldDomain

Represents:

* Ex
* Ey
* Ez
* Hx
* Hy
* Hz

---

## Interface

```python
class FieldDomain(Domain):

    Ex
    Ey
    Ez

    Hx
    Hy
    Hz
```

---

# Source

Represents optical energy injection.

Sources create domains.

Sources do not propagate domains.

---

## Interface

```python
class Source:

    name: str

    def generate(self):
        ...
```

---

## Examples

```python
CWLaser()

PulsedLaser()

GaussianBeam()

PlaneWave()
```

---

# Component

Represents physical optical elements.

Examples:

* Fiber
* Waveguide
* Lens
* Mirror
* Resonator

---

## Interface

```python
class Component:

    name: str

    material: Material

    def metadata(self):
        ...
```

---

# Fiber

Specialized component.

---

## Interface

```python
class Fiber(Component):

    length

    alpha

    gamma

    effective_area

    dispersion
```

---

# Solver

Represents numerical propagation engines.

All solvers must inherit from Solver.

---

## Interface

```python
class Solver:

    name: str

    def build(self):
        ...

    def initialize(self):
        ...

    def solve(self):
        ...

    def postprocess(self):
        ...

    def export(self):
        ...

    def supports(
        self,
        feature
    ):
        ...
```

---

# FourierSolver

Examples:

* Fresnel
* Fraunhofer
* Angular Spectrum

---

# ModeSolver

Outputs:

* neff
* beta
* mode profiles

---

# SSFMSolver

Supports:

* Dispersion
* Kerr
* Nonlinear propagation

---

# GNLSESolver

Supports:

* Raman
* Self-steepening
* Broadband effects

---

# Simulation

Represents a complete simulation.

Responsible for orchestration.

---

## Interface

```python
class Simulation:

    source

    component

    solver

    monitors

    def build(self):
        ...

    def run(self):
        ...

    def result(self):
        ...
```

---

## Example

```python
sim = Simulation(
    source=laser,
    component=fiber,
    solver=gnlse
)

sim.run()
```

---

# SimulationResult

Unified result object.

All solvers must return this type.

---

## Interface

```python
class SimulationResult:

    fields

    spectra

    modes

    observables

    metadata

    def save(self):
        ...

    def export(self):
        ...
```

---

# Observable

Represents derived physical quantities.

Examples:

* Transmission
* Reflection
* Effective Index
* Q Factor
* Bandwidth
* Conversion Efficiency

---

## Interface

```python
class Observable:

    name

    value

    units
```

---

# Experiment

High-level research workflow.

Wraps:

* Sources
* Components
* Solvers
* Diagnostics

---

## Interface

```python
class Experiment:

    name

    def setup(self):
        ...

    def run(self):
        ...

    def analyze(self):
        ...

    def report(self):
        ...
```

---

# Example Experiments

```python
SupercontinuumExperiment()

FWMExperiment()

FiberTransmissionExperiment()

DispersionAnalysisExperiment()
```

---

# Quantity

Strongly typed physical quantity.

Examples:

* Power
* Dispersion
* Attenuation
* Effective Index
* Nonlinear Coefficient

---

## Interface

```python
class Quantity:

    value

    units

    metadata
```

---

# Plugin API

Third-party extensions should register through:

```python
register_material()

register_solver()

register_response()

register_component()

register_experiment()
```

Plugins must extend existing interfaces.

Plugins must not modify core interfaces.

---

# Stability Policy

The following interfaces are considered stable:

* Material
* Response
* Interaction
* Domain
* Source
* Component
* Solver
* Simulation
* SimulationResult
* Observable
* Experiment

New features should extend these interfaces rather than replacing them.

---

# Guiding Principle

Every major object in PhotonicSim should represent a physical concept first and a computational concept second.
