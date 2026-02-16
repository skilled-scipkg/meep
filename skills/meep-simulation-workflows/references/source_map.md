# meep source map: Simulation Workflows

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `dump`
- `far`
- `field`
- `fields`
- `loop`
- `near`
- `output`
- `parallel`
- `run`
- `simulation`
- `step`
- `time`
- `workflow`

## Fast source navigation
- `rg -n "run\(|stop_when_fields_decayed|add_flux|add_mode_monitor|add_near2far|load_minus_flux_data" python/simulation.py`
- `rg -n "fields::step|step_db|step_curl|step_update_EDHB|figure_out_step_plan" src/step.cpp src/step_db.cpp src/step_generic.cpp src/fields.cpp`
- `rg -n "near2far|save_hdf5|load_hdf5|save_farfields" src/near2far.cpp src/h5fields.cpp`

## Suggested source entry points
- `python/simulation.py` | high-level run and monitor API (`run`, `add_flux`, `add_mode_monitor`, `add_near2far`, `load_minus_flux_data`)
- `src/step.cpp` | timestep ordering and boundary/source updates (`fields::step`)
- `src/step_db.cpp` | D/B update stage implementation (`fields::step_db`)
- `src/step_generic.cpp` | low-level update kernels (`step_curl`, `step_update_EDHB`)
- `src/time.cpp` | simulation time accounting and step-time handling
- `src/fields.cpp` | step-plan generation and field state management (`fields::figure_out_step_plan`)
- `src/h5fields.cpp` | field I/O for checkpoints and post-processing
- `src/near2far.cpp` | near2far monitor setup, subtraction, and far-field extraction
- `src/loop_in_chunks.cpp` | chunked iteration path in parallel runs
- `python/tests/test_field_functions.py` | step-function and callback behavior checks
- `python/tests/test_dump_load.py` | restart/checkpoint behavior checks
- `python/tests/test_n2f_periodic.py` | near2far workflow behavior checks

## Function-level behavior checks
- `python python/examples/solve-cw.py`
- `python python/examples/cavity-farfield.py`
- `pytest -q python/tests/test_field_functions.py python/tests/test_dump_load.py`
