---
name: meep-api-and-scripting
description: This skill should be used when users ask about api and scripting in meep; it prioritizes documentation references and then source inspection only for unresolved details.
---

# meep: API and Scripting

## High-Signal Playbook

### Route conditions
- Use this skill for Python API usage patterns: mode decomposition, eigenmode source controls, near2far calls, adjoint workflows, and frequency-domain scripting (`doc/docs/Mode_Decomposition.md`, `doc/docs/Python_Tutorials/Adjoint_Solver.md`).
- Route to `meep-simulation-workflows` for generic run/monitor orchestration.
- Route to `meep-inputs-and-modeling` for material model definitions and geometric parameterization.

### Triage questions
1. Which API feature is central: `get_eigenmode_coefficients`, `add_near2far`, adjoint, or CW solver?
2. Are you comparing power via flux monitors, mode coefficients, or both?
3. Are symmetries enabled, and if so what `eig_parity` should be enforced?
4. Is this a normalization + subtraction workflow?
5. Single frequency (`solve_cw`) or broadband pulse?
6. Is the geometry finite periodic, infinite periodic, or closed-surface radiation?
7. What error tolerance is acceptable (phase/power/far-field)?

### Canonical workflow
1. Select API primitive matching output target (mode coeffs, flux, near2far).
2. Run normalization case and capture monitor data.
3. Run main case and subtract incident data where required.
4. Compute coefficients/fields with explicit parity and direction choices (`doc/docs/Python_Tutorials/Mode_Decomposition.md`).
5. Validate with independent quantity (e.g., compare mode power vs flux).
6. Tighten `resolution`, runtime, and monitor sampling for convergence.

### Minimal working example
```python
import meep as mp

flux = sim.add_flux(fcen, 0, 1, mp.FluxRegion(center=mon_pt, size=mp.Vector3(y=sy)))
sim.run(until_after_sources=mp.stop_when_fields_decayed(50, mp.Ez, mon_pt, 1e-9))
res = sim.get_eigenmode_coefficients(flux, [1], eig_parity=mp.ODD_Z + mp.EVEN_Y)
incident = sim.get_flux_data(flux)

sim.reset_meep()
flux = sim.add_flux(fcen, 0, 1, mp.FluxRegion(center=mon_pt, size=mp.Vector3(y=sy)))
sim.load_minus_flux_data(flux, incident)
sim.run(until_after_sources=mp.stop_when_fields_decayed(50, mp.Ez, mon_pt, 1e-9))
res2 = sim.get_eigenmode_coefficients(flux, [1], eig_parity=mp.ODD_Z + mp.EVEN_Y)
```
- Source: `doc/docs/Python_Tutorials/Mode_Decomposition.md`, `python/examples/mode-decomposition.py`.

### Quick-start commands (repo root)
```bash
python python/examples/mode-decomposition.py
python python/examples/solve-cw.py
pytest -q python/tests/test_mode_coeffs.py python/tests/test_adjoint_solver.py
```

### Pitfalls and fixes
- Missing/incorrect `eig_parity`: MPB may return mixed/degenerate modes (`doc/docs/Python_Tutorials/Mode_Decomposition.md`).
- Skipping subtraction run: finite-resolution non-orthogonality raises reflection noise floor (`doc/docs/Python_Tutorials/Mode_Decomposition.md`).
- Near2far mismatch for open surfaces: truncation error can dominate; enlarge domain or use closed-surface setup (`doc/docs/Python_Tutorials/Near_to_Far_Field_Spectra.md`).
- Finite periodic approximation error: `nperiods` behavior is approximately `O(1/nperiods)`; validate by doubling `nperiods` (`doc/docs/Python_Tutorials/Near_to_Far_Field_Spectra.md`).
- Single-frequency but slow time stepping: evaluate `solve_cw` path (`doc/docs/Python_Tutorials/Frequency_Domain_Solver.md`).

### Convergence and validation checks
- Compare mode-decomposition power and flux-based power; disagreement should shrink with refinement.
- Increase `resolution`, runtime, and angular sampling; far-field error should drop.
- For finite grating approximations, verify error reduction with larger `nperiods`.
- Confirm results with/without symmetry assumptions where feasible.
- If docs are insufficient, inspect `python/solver.py`, `python/simulation.py`, `python/meep.i`, `python/adjoint/optimization_problem.py`, `python/adjoint/objective.py`, `python/adjoint/wrapper.py`, `src/near2far.cpp`, and `scheme/meep_op_renames.i`.

## Scope
- Handle questions about language bindings, APIs, and programmatic interfaces.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `python/examples/README.md`
- `doc/docs/Mode_Decomposition.md`
- `doc/docs/Python_Tutorials/Near_to_Far_Field_Spectra.md`
- `doc/docs/Python_Tutorials/Eigenmode_Source.md`
- `doc/docs/Python_Tutorials/Cylindrical_Coordinates.md`
- `doc/docs/Python_Tutorials/Adjoint_Solver.md`
- `doc/docs/Python_Tutorials/Optical_Forces.md`
- `doc/docs/Python_Tutorials/Frequency_Domain_Solver.md`

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
- `python/simulation.py` | core monitor/solver API (`add_near2far`, `add_mode_monitor`, `solve_cw`, `get_eigenmode_coefficients`, `run`)
- `python/source.py` | source classes (`EigenModeSource`, `CustomSource`, Gaussian/continuous sources)
- `python/solver.py` | MPB-facing mode solver class (`ModeSolver`)
- `python/meep.i` | SWIG API boundary for Python bindings
- `python/adjoint/optimization_problem.py` | adjoint orchestration (`OptimizationProblem.__call__`, `get_objective_arguments`)
- `python/adjoint/objective.py` | objective definitions (`ObjectiveQuantity`)
- `python/adjoint/wrapper.py` | JAX wrapper entry point (`__call__`)
- `python/adjoint/filters.py` | design filters/projections (`conic_filter`, `tanh_projection`)
- `src/near2far.cpp` | near-to-far backend for API monitor calls
- `scheme/meep.scm.in` | Scheme wrappers for monitor/run APIs
- `python/tests/test_mode_coeffs.py` | mode-coefficient API checks
- `python/tests/test_adjoint_solver.py` | adjoint API checks
- `python/tests/test_n2f_periodic.py` | near2far API checks
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
