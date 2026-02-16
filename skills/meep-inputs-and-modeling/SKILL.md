---
name: meep-inputs-and-modeling
description: This skill should be used when users ask about inputs and modeling in meep; it prioritizes documentation references and then source inspection only for unresolved details.
---

# meep: Inputs and Modeling

## High-Signal Playbook

### Route conditions
- Use this skill for geometry/material setup, dispersion/susceptibilities, subpixel smoothing behavior, chunk/symmetry modeling, and GDSII import (`doc/docs/Subpixel_Smoothing.md`, `doc/docs/Materials.md`, `doc/docs/Scheme_User_Interface.md`).
- Route to `meep-simulation-workflows` for monitor/run orchestration questions.
- Route to `meep-api-and-scripting` for advanced API-centric analyses (mode decomposition, adjoint pipelines).

### Triage questions
1. Are materials nondispersive, dispersive, nonlinear, or mixed?
2. Are you defining structure with geometric objects, pixel/grid data, or material functions?
3. Do you need cylindrical coordinates (`m`, `accurate_fields_near_cylorigin`)?
4. Are discontinuities and sharp corners unavoidable?
5. Is performance limited by grid initialization or time stepping?
6. Is the objective field pattern, flux, resonance, or force/LDOS?
7. Which parameters are being swept (`resolution`, geometry, susceptibility terms)?

### Canonical workflow
1. Pick units and characteristic wavelength first (`doc/docs/Introduction.md`, `doc/docs/Units_and_Nonlinearity.md`).
2. Build geometry with analytic objects when possible for better smoothing behavior (`doc/docs/Subpixel_Smoothing.md`).
3. Define material model (`epsilon`, Lorentz/Drude terms, nonlinear coefficients) (`doc/docs/Materials.md`, `doc/docs/Scheme_User_Interface.md`).
4. Add boundaries/symmetries/chunks only after baseline model runs.
5. Run a low-cost resolution sweep to identify dominant discretization error.
6. Validate model choices against tutorial examples (`doc/docs/Python_Tutorials/Material_Dispersion.md`, `python/examples/material-dispersion.py`).

### Minimal working example
```python
import meep as mp

sus = [
    mp.LorentzianSusceptibility(frequency=1.1, gamma=1e-5, sigma=0.5),
    mp.LorentzianSusceptibility(frequency=0.5, gamma=0.1, sigma=2e-5),
]

sim = mp.Simulation(
    cell_size=mp.Vector3(),
    geometry=[],
    sources=[mp.Source(mp.GaussianSource(1.0, fwidth=2.0), mp.Ez, mp.Vector3())],
    default_material=mp.Medium(epsilon=2.25, E_susceptibilities=sus),
    resolution=20,
)
freqs = sim.run_k_points(200, mp.interpolate(9, [mp.Vector3(0.3), mp.Vector3(2.2)]))
```
- Source: `python/examples/material-dispersion.py`, `doc/docs/Python_Tutorials/Material_Dispersion.md`.

### Quick-start commands (repo root)
```bash
python python/examples/material-dispersion.py
python python/examples/refl-angular.py
pytest -q python/tests/test_material_dispersion.py python/tests/test_medium_evaluations.py
```

### Pitfalls and fixes
- `Convergence is first-order despite smoothing`: dispersive interfaces are not subpixel-averaged; expect slower convergence (`doc/docs/Subpixel_Smoothing.md`).
- `Initialization is slow`: material-function averaging is expensive; tune `subpixel_tol`/`subpixel_maxeval` (`doc/docs/Subpixel_Smoothing.md`).
- `Pixel/grid and analytic geometry disagree`: they are different structures at finite resolution; compare like-for-like (`doc/docs/Subpixel_Smoothing.md`).
- `Instability in cylindrical mode m>1`: reduce `Courant` when `accurate_fields_near_cylorigin=True` (`doc/docs/Python_User_Interface.md`).
- `Sharp-corner artifacts`: expect slower/irregular convergence; refine grid and validate by trend (`doc/docs/Subpixel_Smoothing.md`).

### Convergence and validation checks
- Sweep `resolution`; verify monotonic trend of target metrics.
- Compare `eps_averaging=True` vs `False` to identify interface-dominated error.
- Check if alternate geometry representations converge to intended structure.
- For dispersive fits, ensure frequencies of interest are inside fit-valid band.
- If docs are insufficient, inspect `python/materials.py`, `scheme/materials.scm`, `src/material_data.hpp`, `src/material_data.cpp`, `src/susceptibility.cpp`, `src/structure.cpp`, `src/loop_in_chunks.cpp`, and `src/GDSIIgeom.cpp`.

## Scope
- Handle questions about inputs, system setup, models, and physical parameterization.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `doc/docs/Subpixel_Smoothing.md`
- `doc/docs/Scheme_User_Interface.md`
- `doc/docs/Materials.md`
- `doc/docs/Chunks_and_Symmetry.md`
- `doc/docs/Scheme_Tutorials/Material_Dispersion.md`
- `doc/docs/Scheme_Tutorials/Casimir_Forces.md`
- `doc/docs/Python_Tutorials/Third_Harmonic_Generation.md`
- `doc/docs/Python_Tutorials/Resonant_Modes_and_Transmission_in_a_Waveguide_Cavity.md`
- `doc/docs/Python_Tutorials/Mode_Decomposition.md`
- `doc/docs/Python_Tutorials/Material_Dispersion.md`
- `doc/docs/Python_Tutorials/Local_Density_of_States.md`
- `doc/docs/Python_Tutorials/GDSII_Import.md`

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
- `python/materials.py` | material-library definitions and medium helpers
- `scheme/materials.scm` | Scheme material definitions for ctl inputs
- `scheme/structure.cpp` | Scheme-to-C++ structure/material translation (`make_structure`, `get_chi3`)
- `src/structure.cpp` | geometry voxelization and structure update path
- `src/material_data.hpp` | coefficient storage (`E_chi2_diag`, `E_chi3_diag`, conductivities)
- `src/material_data.cpp` | material initialization/copy behavior (`material_data::material_data`, `copy_from`)
- `src/susceptibility.cpp` | dispersive and gyrotropic polarization updates
- `src/loop_in_chunks.cpp` | chunk iteration behavior for geometry/material operations
- `src/GDSIIgeom.cpp` | GDSII import and polygon conversion
- `src/casimir.cpp` | Casimir source/material coupling path
- `python/tests/test_material_dispersion.py` | dispersive-material regression checks
- `python/tests/test_chunks.py` | chunk/symmetry modeling checks
- `python/tests/test_multilevel_atom.py` | multilevel susceptibility checks
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
