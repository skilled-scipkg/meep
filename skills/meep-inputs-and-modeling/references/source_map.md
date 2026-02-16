# meep source map: Inputs and Modeling

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `boundary`
- `casimir`
- `chunks`
- `dispersion`
- `gdsii`
- `geometry`
- `input`
- `material`
- `materials`
- `model`
- `modeling`
- `multilevel`
- `susceptibility`

## Fast source navigation
- `rg -n "material|susceptibility|chi2|chi3|subpixel|GDSII|multilevel|casimir" python scheme src`
- `rg -n "make_structure|get_chi3|E_chi3_diag|H_chi3_diag|loop_in_chunks" scheme/structure.cpp src/material_data.hpp src/loop_in_chunks.cpp`
- `rg -n "class|def|struct|namespace" libpympb python scheme src`

## Suggested source entry points
- `python/materials.py` | material-library definitions and convenience constructors
- `scheme/materials.scm` | Scheme material definitions used in ctl workflows
- `scheme/structure.cpp` | Scheme-to-C++ translation of geometry/material inputs (`make_structure`, `get_chi3`)
- `src/structure.cpp` | geometry voxelization and structure update pipeline
- `src/material_data.hpp` | material/nonlinear coefficient layout (`E_chi2_diag`, `E_chi3_diag`, conductivities)
- `src/material_data.cpp` | material initialization and copy behavior (`material_data::material_data`, `copy_from`)
- `src/susceptibility.cpp` | dispersive and gyrotropic polarization updates
- `src/loop_in_chunks.cpp` | chunk iteration used by geometry/material update paths
- `src/GDSIIgeom.cpp` | GDSII parsing and geometry conversion
- `src/casimir.cpp` | Casimir-force source/material coupling path
- `python/tests/test_material_dispersion.py` | dispersive-material regression checks
- `python/tests/test_medium_evaluations.py` | medium-property evaluation checks
- `python/tests/test_chunks.py` | chunk/symmetry behavior checks
- `python/tests/test_multilevel_atom.py` | multilevel susceptibility checks

## Function-level behavior checks
- `python python/examples/material-dispersion.py`
- `python python/examples/refl-angular.py`
- `pytest -q python/tests/test_material_dispersion.py python/tests/test_medium_evaluations.py`
