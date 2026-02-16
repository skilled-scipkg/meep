# meep source map: Getting Started

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `angle`
- `basics`
- `bfast`
- `broadband`
- `fixed`
- `getting`
- `intro`
- `introduction`
- `overview`
- `quickstart`
- `simulations`
- `started`

## Fast source navigation
- `rg -n "<symbol_or_keyword>" libpympb python scheme src`
- `rg -n "class Simulation|def run|def add_flux|def stop_when_fields_decayed" python/simulation.py`
- `rg -n "fields::step|step_source|step_update_EDHB|step_curl" src/step.cpp src/step_generic.cpp`

## Suggested source entry points
- `python/simulation.py` | first-call control path (`Simulation.__init__`, `run`, `add_flux`, `reset_meep`, `stop_when_fields_decayed`)
- `python/source.py` | source definitions used by starter examples (`ContinuousSource`, `GaussianSource`, `EigenModeSource`)
- `python/meep.i` | Python/SWIG boundary for beginner-facing APIs
- `src/step.cpp` | timestep orchestration (`fields::step`, `step_source`, `step_boundaries`)
- `src/step_generic.cpp` | low-level field updates (`step_curl`, `step_update_EDHB`)
- `src/fields.cpp` | step-planning and field state management (`fields::figure_out_step_plan`)
- `src/fix_boundary_sources.cpp` | source correction near boundaries/PML
- `python/tests/test_simulation.py` | end-to-end behavior checks for setup and stepping
- `python/tests/test_refl_angular.py` | starter reflectance workflow regression check

## Function-level behavior checks
- `python python/examples/straight-waveguide.py`
- `python python/examples/refl-angular.py`
- `pytest -q python/tests/test_simulation.py`
