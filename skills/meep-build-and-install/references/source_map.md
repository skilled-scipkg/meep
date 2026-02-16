# meep source map: Build and Install

Generated from source roots:
- `libpympb`
- `python`
- `scheme`
- `src`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Topic query tokens
- `autotools`
- `build`
- `compile`
- `configure`
- `dependencies`
- `hdf5`
- `install`
- `link`
- `make`
- `mpi`
- `rpath`
- `swig`
- `toolchain`

## Fast source navigation
- `rg -n "AC_INIT|AM_INIT_AUTOMAKE|SUBDIRS|lib_LTLIBRARIES|check_PROGRAMS" src python scheme libpympb tests`
- `rg -n "meep.i|typemap|%module|%include" python/meep.i python/typemap_utils.cpp`
- `rg -n "PyInit|init|meep" python/meep-python.hpp scheme/meep.cpp`

## Suggested source entry points
- `src/Makefile.am` | core `libmeep` targets and dependency linkage
- `src/support/Makefile.am` | support-library objects required for successful linking
- `python/Makefile.am` | Python module build/install targets and SWIG artifacts
- `libpympb/Makefile.am` | MPB linkage used by mode/eigensolver features
- `scheme/Makefile.am` | Scheme/Guile binding targets and install hooks
- `python/meep.i` | SWIG declarations exposing C++ APIs to Python
- `python/meep-python.hpp` | C++ helper types used by Python wrappers
- `python/typemap_utils.cpp` | Python<->C++ typemap conversion helpers
- `scheme/meep.cpp` | Guile/Scheme module entry points
- `tests/Makefile.am` | `make check` target wiring
- `python/tests/test_simulation.py` | post-install Python import/runtime smoke check
- `tests/known_results.cpp` | C++ regression entry point for binary/runtime verification

## Function-level behavior checks
- `python -c "import meep as mp; print(mp.__version__)"`
- `python python/examples/straight-waveguide.py`
- `pytest -q python/tests/test_simulation.py`
