# meep source map: Examples and Tutorials

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `cookbook`
- `custom`
- `decomposition`
- `domain`
- `eigenmode`
- `frequency`
- `gyrotropic`
- `howto`
- `media`
- `mode`
- `solver`
- `walkthrough`

## Fast source navigation
- `rg -n "mode[-_ ]decomposition|eigenmode|near2far|solve_cw|gyrotropic|CustomSource" python/examples scheme/examples python src`
- `rg -n "add_mode_monitor|get_eigenmode_coefficients|add_near2far|solve_cw" python/simulation.py scheme/meep.scm.in`
- `rg -n "class|def|struct|namespace" libpympb python scheme src`

## Suggested source entry points
- `python/examples/mode-decomposition.py` | reference script for mode-coefficient workflows
- `python/examples/binary_grating_n2f.py` | near2far tutorial implementation
- `python/examples/solve-cw.py` | frequency-domain tutorial script
- `python/examples/faraday-rotation.py` | gyrotropic-media tutorial script
- `scheme/examples/mode-decomposition.ctl` | Scheme counterpart for mode decomposition tutorial
- `scheme/examples/binary_grating_n2f.ctl` | Scheme near2far tutorial
- `python/simulation.py` | tutorial-facing API methods (`add_flux`, `add_mode_monitor`, `add_near2far`, `solve_cw`)
- `scheme/meep.scm.in` | Scheme wrappers used in tutorial ctl files (`add-mode-monitor`, `add-near2far`, `run-sources`)
- `src/near2far.cpp` | far-field transform backend (`fields::add_dft_near2far`, `dft_near2far::farfield`)
- `src/cw_fields.cpp` | CW solver backend used by `solve-cw`
- `python/tests/test_mode_decomposition.py` | tutorial regression for mode decomposition
- `python/tests/test_n2f_periodic.py` | tutorial regression for near2far periodic setups
- `python/tests/test_faraday_rotation.py` | tutorial regression for gyrotropic behavior

## Function-level behavior checks
- `python python/examples/mode-decomposition.py`
- `python python/examples/binary_grating_n2f.py`
- `pytest -q python/tests/test_mode_decomposition.py python/tests/test_n2f_periodic.py`
