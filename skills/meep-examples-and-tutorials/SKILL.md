---
name: meep-examples-and-tutorials
description: This skill should be used when users ask about examples and tutorials in meep; it prioritizes documentation references and then source inspection only for unresolved details.
---

# meep: Examples and Tutorials

## High-Signal Playbook

### Route conditions
- Use this skill when the user asks for runnable reference scripts, reproducible command sequences, or cookbook-style adaptation from official tutorials (`doc/docs/Scheme_Tutorials`, `doc/docs/Python_Tutorials`).
- Route to `meep-api-and-scripting` for deep API behavior interpretation.
- Route to `meep-simulation-workflows` for monitor/run control design.

### Triage questions
1. Should the example be `Scheme` (`meep`) or `Python` (`import meep as mp`)?
2. Which phenomenon is closest: mode decomposition, eigenmode source, custom source, gyrotropic media, or frequency-domain solver?
3. Do you need spectra, field visualizations, phase, or S-parameters?
4. Is a normalization run required for the quantity of interest?
5. What parameter should be swept first for sensitivity?
6. Is symmetry enabled and compatible with monitor choice?

### Canonical workflow
1. Pick the nearest tutorial from `references/doc_map.md`.
2. Run the example unchanged and capture output.
3. Extract key output channels (`flux`, mode coeff, printed diagnostics).
4. Change one parameter at a time and keep baseline output for comparison.
5. Validate against tutorial trends/analytic expectations.
6. If behavior diverges, inspect linked implementation files.

### Minimal working example
```bash
m=6
mpirun -np 2 meep Lt=$((2**m)) scheme/examples/mode-decomposition.ctl | tee waveguide_taper.out
mpirun -np 2 meep scheme/examples/binary_grating.ctl | tee diffraction_spectra.out
python python/examples/mode-decomposition.py

grep flux1: waveguide_taper.out | cut -d, -f2- > flux.dat
grep guided: waveguide_taper.out | cut -d, -f2- > guided.dat
```
- Source: `skills/.evidence/meep-examples-and-tutorials.md`, `doc/docs/Scheme_Tutorials/Mode_Decomposition.md`.

### Pitfalls and fixes
- Reflection extraction too noisy: use normalization + `load-minus-flux` workflow (`doc/docs/Scheme_Tutorials/Mode_Decomposition.md`).
- Wrong diffraction/mode parity: set `eig-parity` explicitly under symmetry (`doc/docs/Scheme_Tutorials/Mode_Decomposition.md`).
- Oblique planewave does not decay: narrow bandwidth or use fixed runtime for glancing-angle cases (`doc/docs/Scheme_Tutorials/Mode_Decomposition.md`).
- Frequency features unresolved: increase frequency sampling and runtime (`doc/docs/Scheme_Tutorials/Custom_Source.md`).
- Wrong monitor type with symmetry: prefer tutorial-recommended monitor choice for that setup.

### Convergence and validation checks
- Increase runtime by ~2x and verify spectral peaks/coefficients stabilize.
- Refine frequency grid and check whether trends are preserved.
- Sweep `resolution`; discretization-sensitive errors should decrease.
- For mode decomposition examples, compare flux and mode-coefficient power agreement.
- If docs are insufficient, inspect `scheme/meep.scm.in`, `scheme/meep_op_renames.i`, `src/sources.cpp`, `src/fields.cpp`, `src/cw_fields.cpp`, and `python/examples/mode-decomposition.py`.

## Scope
- Handle questions about worked examples, tutorials, and cookbook usage.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `doc/docs/Scheme_Tutorials/Mode_Decomposition.md`
- `doc/docs/Scheme_Tutorials/Eigenmode_Source.md`
- `doc/docs/Scheme_Tutorials/Custom_Source.md`
- `doc/docs/Scheme_Tutorials/Gyrotropic_Media.md`
- `doc/docs/Scheme_Tutorials/Frequency_Domain_Solver.md`

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
- `python/examples/mode-decomposition.py` | reference script for mode-coefficient workflows
- `python/examples/binary_grating_n2f.py` | near2far tutorial script
- `python/examples/solve-cw.py` | frequency-domain tutorial script
- `python/examples/faraday-rotation.py` | gyrotropic-media tutorial script
- `scheme/examples/mode-decomposition.ctl` | Scheme mode decomposition reference
- `scheme/examples/binary_grating_n2f.ctl` | Scheme near2far reference
- `python/simulation.py` | tutorial-facing API methods (`add_flux`, `add_mode_monitor`, `add_near2far`, `solve_cw`)
- `scheme/meep.scm.in` | Scheme wrappers used in tutorial ctl files (`add-mode-monitor`, `add-near2far`, `run-sources`)
- `src/near2far.cpp` | far-field backend (`fields::add_dft_near2far`, `dft_near2far::farfield`)
- `src/cw_fields.cpp` | frequency-domain backend used by `solve-cw`
- `python/tests/test_mode_decomposition.py` | mode decomposition tutorial checks
- `python/tests/test_n2f_periodic.py` | near2far tutorial checks
- `python/tests/test_faraday_rotation.py` | gyrotropic tutorial checks
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
