---
name: meep-simulation-workflows
description: This skill should be used when users ask about simulation workflows in meep; it prioritizes documentation references and then source inspection only for unresolved details.
---

# meep: Simulation Workflows

## High-Signal Playbook

### Route conditions
- Use this skill for run control, step functions, normalization/scattering orchestration, output/restart patterns, and near2far monitor setup (`doc/docs/The_Run_Function_Is_Not_A_Loop.md`, `doc/docs/Python_User_Interface.md`, `doc/docs/Field_Functions.md`).
- Route to `meep-inputs-and-modeling` for geometry/material-model changes.
- Route to `meep-api-and-scripting` for advanced eigenmode/adjoint scripts.

### Triage questions
1. Time-domain stepping or frequency-domain solve (`solve_cw`)?
2. What outputs are required (fields, flux, mode coefficients, far fields)?
3. Are you doing one run or normalization + main run?
4. Do you need fixed runtime or `stop_when_fields_decayed`?
5. Are near2far surfaces fully in homogeneous non-PML media?
6. Which parallel mode is used (serial/MPI), and are outputs rank-safe?
7. Are you using symmetries that affect monitor compatibility?

### Canonical workflow
1. Build `Simulation` with stable baseline settings (`doc/docs/Python_User_Interface.md`).
2. Add monitors (`add_flux`, `add_mode_monitor`, `add_near2far`) before running.
3. Run normalization case; store monitor data (`get_flux_data`).
4. Reset simulation, load subtraction data (`load_minus_flux_data`), run main case.
5. Use step wrappers (`at_every`, `at_beginning`, `to_appended`) instead of manual loop assumptions (`doc/docs/The_Run_Function_Is_Not_A_Loop.md`).
6. For near2far, ensure closed/adequate surfaces and correct region weights (`doc/docs/Python_User_Interface.md`, `doc/docs/Scheme_Tutorials/Near_to_Far_Field_Spectra.md`).

### Minimal working example
```python
import meep as mp

def my_hello(sim):
    print("step")

mon_pt = mp.Vector3()
sim.run(
    mp.at_every(1, my_hello),
    until_after_sources=mp.stop_when_fields_decayed(50, mp.Ez, mon_pt, 1e-9),
)
```
- Step-function semantics source: `doc/docs/The_Run_Function_Is_Not_A_Loop.md`.

### Quick-start commands (repo root)
```bash
python python/examples/solve-cw.py
python python/examples/cavity-farfield.py
pytest -q python/tests/test_field_functions.py python/tests/test_dump_load.py
```

### Pitfalls and fixes
- `run` treated like a loop: pass callable step functions, not function results (`doc/docs/The_Run_Function_Is_Not_A_Loop.md`).
- `Near2Far` mismatch: surfaces must lie in one homogeneous/isotropic medium and outside PML (`doc/docs/Python_User_Interface.md`).
- Wrong near2far signs: set outward `weight` signs consistently on each face (`doc/docs/Python_User_Interface.md`).
- Early termination broadens spectra: run longer or tighten decay threshold (`doc/docs/FAQ.md`).
- Courant instability: keep `Courant` within stability limits (`doc/docs/Python_User_Interface.md`).

### Convergence and validation checks
- Double runtime (or lower decay criterion) and check output stability.
- Compare near-vs-far total power where applicable; error should shrink with refinement.
- Increase `resolution`; monitor quantities should converge.
- Re-run with larger padding/PML and verify reduced boundary artifacts.
- If docs are insufficient, inspect `python/simulation.py`, `src/time.cpp`, `src/step.cpp`, `src/step_db.cpp`, `src/step_generic.cpp`, `src/fields.cpp`, `src/fields_dump.cpp`, `src/h5fields.cpp`, and `src/near2far.cpp`.

## Scope
- Handle questions about simulation setup, execution flow, and runtime controls.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `doc/docs/index.md`
- `doc/docs/The_Run_Function_Is_Not_A_Loop.md`
- `doc/docs/Python_User_Interface.md`
- `doc/docs/Python_Developer_Information.md`
- `doc/docs/Parallel_Meep.md`
- `doc/docs/Field_Functions.md`
- `doc/docs/Synchronizing_the_Magnetic_and_Electric_Fields.md`
- `doc/docs/Scheme_Tutorials/Near_to_Far_Field_Spectra.md`
- `doc/docs/Python_Tutorials/Gyrotropic_Media.md`

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
- `python/simulation.py` | high-level run/monitor API (`run`, `add_flux`, `add_mode_monitor`, `add_near2far`, `load_minus_flux_data`)
- `src/step.cpp` | timestep ordering and boundary/source updates (`fields::step`)
- `src/step_db.cpp` | D/B update stage (`fields::step_db`)
- `src/step_generic.cpp` | low-level update kernels (`step_curl`, `step_update_EDHB`)
- `src/time.cpp` | time-accounting and step-time behavior
- `src/fields.cpp` | step-plan generation (`fields::figure_out_step_plan`)
- `src/h5fields.cpp` | checkpoint and field-output I/O
- `src/near2far.cpp` | near2far setup/subtraction and far-field extraction
- `src/loop_in_chunks.cpp` | chunked traversal in parallel workflows
- `python/tests/test_field_functions.py` | step-function callback behavior checks
- `python/tests/test_dump_load.py` | restart/checkpoint behavior checks
- `python/tests/test_n2f_periodic.py` | near2far workflow checks
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
