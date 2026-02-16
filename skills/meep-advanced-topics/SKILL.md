---
name: meep-advanced-topics
description: This skill should be used when users need one of the narrow advanced topics consolidated from single-doc skills (special-kz, symmetry, PML, Yee lattice, units/nonlinearity, eigensolver math, Scheme details, legal/credits).
---

# meep: Advanced Topics

## Scope
- Consolidated routing for low-volume one-doc topics to keep the skill set compact.
- Use this skill when the request is specifically about one of the advanced references below.

## Route the request
- `2d Cell with Out-of-Plane Wavevector`: `doc/docs/2d_Cell_Special_kz.md`
- `Exploiting Symmetry`: `doc/docs/Exploiting_Symmetry.md`
- `Perfectly Matched Layer`: `doc/docs/Perfectly_Matched_Layer.md`
- `Yee Lattice`: `doc/docs/Yee_Lattice.md`
- `Units and Nonlinearity`: `doc/docs/Units_and_Nonlinearity.md`
- `Frequency-Domain Eigensolver`: `doc/docs/Eigensolver_Math.md`
- `Guile and Scheme Information`: `doc/docs/Guile_and_Scheme_Information.md`
- `License and Copyright`: `doc/docs/License_and_Copyright.md`
- `Acknowledgements`: `doc/docs/Acknowledgements.md`

## Workflow
- Open the specific advanced doc first; keep answers doc-backed and concise.
- If implementation details are needed, use `references/source_map.md` for concrete source entry points.
- For setup/run/modeling questions, route back to the corresponding core skill.

## Quick verification commands (repo root)
```bash
pytest -q python/tests/test_special_kz.py python/tests/test_pml_cyl.py
rg -n "mirror|rotate2|rotate4" src/vec.cpp
```

## Related core skills
- `meep-getting-started`
- `meep-build-and-install`
- `meep-inputs-and-modeling`
- `meep-simulation-workflows`
- `meep-api-and-scripting`
- `meep-examples-and-tutorials`

## Source entry points for unresolved issues
- `scheme/structure.cpp` | advanced structure setup (`make_structure`, `get_chi3`, `scm_pml_profile*`)
- `scheme/meep.scm.in` | Scheme parameters/wrappers (`pml-layers`, `Courant`, `add-near2far`)
- `scheme/meep_op_renames.i` | Scheme operator/API exposure for symmetry/math calls
- `src/vec.cpp` | symmetry transforms (`mirror`, `rotate2`, `rotate4`)
- `src/step_generic.cpp` | Yee update/nonlinear kernels (`step_curl`, `step_update_EDHB`)
- `src/loop_in_chunks.cpp` | chunked iteration path used by symmetry/chunk decomposition
- `src/material_data.hpp` | nonlinear/conductive coefficient layout (`E_chi3_diag`, `H_chi3_diag`)
- `src/material_data.cpp` | material-data initialization and copy behavior (`material_data::material_data`, `copy_from`)
- `src/susceptibility.cpp` | dispersive and gyrotropic susceptibility updates (`update_P`)
- `python/tests/test_special_kz.py` | special-kz behavior checks
- `python/tests/test_pml_cyl.py` | cylindrical PML behavior checks
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
