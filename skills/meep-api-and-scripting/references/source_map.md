# meep source map: API and Scripting

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `adjoint`
- `bindings`
- `coordinates`
- `decomposition`
- `eigenmode`
- `far`
- `field`
- `forces`
- `frequency`
- `interface`
- `mode`
- `near`
- `scripting`
- `solver`

## Fast source navigation
- `rg -n "<symbol_or_keyword>" libpympb python scheme src`
- `rg -n "add_mode_monitor|get_eigenmode_coefficients|add_near2far|solve_cw|run_k_points" python/simulation.py python/solver.py`
- `rg -n "OptimizationProblem|ObjectiveQuantity|conic_filter|tanh_projection" python/adjoint`

## Suggested source entry points
- `python/simulation.py` | core Python API behavior (`add_near2far`, `add_mode_monitor`, `solve_cw`, `get_eigenmode_coefficients`, `run`)
- `python/source.py` | source-object behavior (`EigenModeSource`, `CustomSource`, Gaussian/continuous source classes)
- `python/solver.py` | MPB-facing mode-solver API (`ModeSolver`)
- `python/meep.i` | SWIG interface boundary for Python bindings
- `python/adjoint/optimization_problem.py` | high-level adjoint orchestration (`OptimizationProblem.__call__`, `get_objective_arguments`)
- `python/adjoint/objective.py` | objective quantity definitions and evaluation hooks (`ObjectiveQuantity`)
- `python/adjoint/wrapper.py` | JAX-backed optimization wrapper entry point (`__call__`)
- `python/adjoint/filters.py` | design filtering/projection helpers (`conic_filter`, `tanh_projection`)
- `src/near2far.cpp` | near-to-far backend and monitor subtraction paths
- `scheme/meep.scm.in` | Scheme API wrappers for monitor and run primitives
- `python/tests/test_mode_coeffs.py` | mode-coefficient API regression checks
- `python/tests/test_adjoint_solver.py` | adjoint workflow regression checks
- `python/tests/test_n2f_periodic.py` | near2far API regression checks

## Function-level behavior checks
- `python python/examples/mode-decomposition.py`
- `python python/examples/solve-cw.py`
- `pytest -q python/tests/test_mode_coeffs.py python/tests/test_adjoint_solver.py`
