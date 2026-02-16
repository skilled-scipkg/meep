---
name: meep-getting-started
description: This skill should be used when users ask about getting started in meep; it prioritizes documentation references and then source inspection only for unresolved details.
---

# meep: Getting Started

## High-Signal Playbook

### Route conditions
- Use this skill for first runnable simulations, basic units, first field plots, and first reflectance/transmittance workflows (`doc/docs/Introduction.md`, `doc/docs/Python_Tutorials/Basics.md`, `doc/docs/Scheme_Tutorials/Basics.md`).
- Route to `meep-build-and-install` for environment, compiler, MPI, or install failures.
- Route to `meep-inputs-and-modeling` for material models, subpixel smoothing, and geometry parameterization.
- Route to `meep-simulation-workflows` for run/step function control, monitor orchestration, and restart/output behavior.
- Route to `meep-api-and-scripting` for eigenmode/adjoint/near2far-heavy API questions.

### Triage questions
1. Which interface do you need now: `Python`, `Scheme`, or `C++`?
2. What is the first target observable: field snapshot, flux spectrum, resonance, or far-field pattern?
3. What simulation dimensionality are you using (1d/2d/3d), and what is the highest index material?
4. Are you using `ContinuousSource` or `GaussianSource`?
5. Where are PML and monitors relative to source and scatterer?
6. Do you need normalized spectra (two runs) or only a direct field view?
7. What runtime/resolution budget is acceptable?

### Canonical workflow
1. Start from a known baseline (`python/examples/straight-waveguide.py` or `scheme/examples/straight-waveguide.ctl`).
2. Set `cell_size`, `geometry`, `sources`, `boundary_layers`, and `resolution` using tutorial defaults (`doc/docs/Python_Tutorials/Basics.md`).
3. Run once to sanity-check propagation and absorption.
4. Add flux monitors within source bandwidth for spectra (`doc/docs/Introduction.md`).
5. For reflectance/transmittance, run normalization + scattering with identical discretization and monitor placement (`doc/docs/Introduction.md`).
6. Sweep runtime/resolution until key metrics stabilize (`doc/docs/FAQ.md`).

### Minimal working example
```python
import meep as mp

cell = mp.Vector3(16, 8, 0)
geometry = [mp.Block(mp.Vector3(mp.inf, 1, mp.inf), material=mp.Medium(epsilon=12))]
sources = [mp.Source(mp.ContinuousSource(frequency=0.15), mp.Ez, mp.Vector3(-7, 0))]

sim = mp.Simulation(
    cell_size=cell,
    geometry=geometry,
    sources=sources,
    boundary_layers=[mp.PML(1.0)],
    resolution=10,
)
sim.run(until=200)
```
- Source: `doc/docs/Python_Tutorials/Basics.md`, `python/examples/straight-waveguide.py`.

### Quick-start commands (repo root)
```bash
python python/examples/straight-waveguide.py
python python/examples/refl-angular.py
pytest -q python/tests/test_simulation.py
```

### Pitfalls and fixes
- `Results change sharply with resolution`: increase resolution and compare trends; coarse-interface error dominates early (`doc/docs/Introduction.md`).
- `Spectra are noisy or shifted`: run longer or tighten decay criteria (`doc/docs/FAQ.md`).
- `PML not absorbing well`: remember PML is inside the cell; increase thickness/padding (`doc/docs/Python_Tutorials/Basics.md`).
- `Reflectance normalization inconsistent`: keep identical source/monitor geometry and discretization between runs (`doc/docs/Introduction.md`).
- `Out-of-band flux looks random`: trust frequencies near source bandwidth only (`doc/docs/Python_Tutorials/Basics.md`).

### Convergence and validation checks
- Double `resolution`; target quantities should move toward a stable value.
- Increase runtime (or lower decay threshold) until spectra stop drifting.
- Check basic energy balance trend (`R + T + loss`) and whether mismatch shrinks with refinement.
- Increase PML thickness; reflected artifacts should decrease.
- If docs are insufficient, inspect `python/simulation.py`, `src/fields.cpp`, `src/step.cpp`, `src/step_generic.cpp`, and `src/fix_boundary_sources.cpp`.

## Scope
- Handle questions about initial setup, quickstarts, and core concepts.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `doc/docs/Introduction.md`
- `doc/docs/C++_Tutorial.md`
- `doc/bfast/fixed_angle_broadband_simulations_in_Meep.md`
- `doc/docs/Scheme_Tutorials/Basics.md`
- `doc/docs/Python_Tutorials/Basics.md`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use tutorials/examples as executable usage patterns when available.
- Use tests as behavior or regression references when available.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the ranked source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `python/examples`
- `scheme/examples`
- `doc/docs/Python_Tutorials`
- `doc/docs/Scheme_Tutorials`

## Test references
- `tests`
- `python/tests`

## Optional deeper inspection
- `libpympb`
- `python`
- `scheme`
- `src`

## Source entry points for unresolved issues
- `python/simulation.py` | `Simulation.__init__`, `run`, `add_flux`, `reset_meep`, `stop_when_fields_decayed`
- `python/source.py` | `ContinuousSource`, `GaussianSource`, `EigenModeSource`
- `python/meep.i` | Python/SWIG binding layer for simulation/source APIs
- `src/step.cpp` | `fields::step`, `step_source`, `step_boundaries`
- `src/step_generic.cpp` | `step_curl`, `step_update_EDHB`
- `src/fields.cpp` | `fields::figure_out_step_plan`
- `src/fix_boundary_sources.cpp` | source correction at boundaries/PML
- `python/tests/test_simulation.py` | startup simulation regression checks
- `python/tests/test_refl_angular.py` | starter reflectance workflow check
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
