# meep source map: Advanced Topics

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `absorber`
- `chi3`
- `courant`
- `eigensolver`
- `kz_2d`
- `mirror`
- `nonlinearity`
- `pml`
- `rotate`
- `special_kz`
- `susceptibility`
- `yee`

## Fast source navigation
- `rg -n "<symbol_or_keyword>" libpympb python scheme src`
- `rg -n "special_kz|kz_2d|pml|absorber|Courant|chi3|susceptibility|rotate2|rotate4|mirror" scheme src python/tests`
- `rg -n "class|def|struct|namespace" libpympb python scheme src`

## Suggested source entry points
- `scheme/structure.cpp` | advanced structure setup (`make_structure`, `get_chi3`, `scm_pml_profile*`) and special-kz wiring
- `scheme/meep.scm.in` | Scheme parameters/wrappers (`pml-layers`, `Courant`, `add-near2far`, step wrappers)
- `scheme/meep_op_renames.i` | Scheme API operator exposure for geometry/symmetry/math calls
- `src/vec.cpp` | symmetry transforms (`mirror`, `rotate2`, `rotate4`) used by advanced symmetry flows
- `src/step_generic.cpp` | Yee update kernels with nonlinear path (`step_curl`, `step_update_EDHB`)
- `src/loop_in_chunks.cpp` | chunked loop behavior under symmetry/chunk decomposition (`fields::loop_in_chunks`)
- `src/material_data.hpp` | nonlinear/conductive coefficient storage (`E_chi3_diag`, `H_chi3_diag`)
- `src/material_data.cpp` | material-data initialization and copy behavior (`material_data::material_data`, `copy_from`)
- `src/susceptibility.cpp` | dispersive/gyrotropic susceptibility updates (`lorentzian_susceptibility::update_P`, `gyrotropic_susceptibility::update_P`)
- `python/tests/test_special_kz.py` | special-kz behavior checks
- `python/tests/test_pml_cyl.py` | cylindrical PML behavior checks

## Function-level behavior checks
- `pytest -q python/tests/test_special_kz.py python/tests/test_pml_cyl.py`
- `rg -n "mirror|rotate2|rotate4" src/vec.cpp`
