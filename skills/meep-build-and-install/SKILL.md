---
name: meep-build-and-install
description: This skill should be used when users ask about build and install in meep; it prioritizes documentation references and then source inspection only for unresolved details.
---

# meep: Build and Install

## High-Signal Playbook

### Route conditions
- Use this skill for install method choice (`conda` vs source), compiler/MPI/HDF5 setup, and import/runtime-link failures (`doc/docs/Installation.md`, `doc/docs/Build_From_Source.md`).
- Route to `meep-getting-started` after installation succeeds and user needs first simulation.
- Route to `meep-simulation-workflows` for runtime monitor logic (not build issues).

### Triage questions
1. Which OS and architecture are you on?
2. Do you need Python only, or Python + Scheme?
3. Do you need MPI/parallel execution now?
4. Are you allowed to use Conda, or must you build from source?
5. Do you have root access, or do you need a `--prefix` install?
6. Are you mixing compilers/libraries from different toolchains?
7. Is the failure at `configure`, `make`, `import meep`, or runtime (`mpirun`)?

### Canonical workflow
1. Prefer Conda for standard Python usage (`doc/docs/Installation.md`).
2. Create environment, activate, verify `import meep` and `mp.__version__`.
3. If MPI is needed, install MPI build string and test with `mpirun` (`doc/docs/Installation.md`).
4. If source build is required, configure with explicit paths/toolchain (`doc/docs/Build_From_Source.md`).
5. Run `make`/`make check`; fix linker/path/toolchain mismatches.
6. Keep one consistent compiler family across dependencies, especially on HPC (`doc/docs/Build_From_Source.md`).

### Minimal working example
```bash
conda create -n mp -c conda-forge pymeep
conda activate mp
python -c "import meep as mp; print(mp.__version__)"

conda create -n pmp -c conda-forge pymeep=*=mpi_mpich_*
conda activate pmp
mpirun -np 4 python <script_name>.py
```
- Source: `doc/docs/Installation.md`.

### Pitfalls and fixes
- `illegal instruction` on import: pin OpenBLAS to `0.3.4` (`doc/docs/Installation.md`).
- MPB crashes after installing other packages: prevent MKL/OpenBLAS mix; stay on `conda-forge` (`doc/docs/Installation.md`).
- Source build finds wrong libs: pass explicit `LDFLAGS`/`CPPFLAGS` and runtime rpath (`doc/docs/Build_From_Source.md`).
- MPI/HDF5 mismatch on clusters: compile/link consistently with MPI toolchain (`doc/docs/Build_From_Source.md`).
- Multiple compiler vendors on supercomputers: pick one stack and keep it consistent (`doc/docs/Build_From_Source.md`).

### Convergence and validation checks
- `python -c "import meep"` passes in target environment.
- `python -c "import meep as mp; print(mp.__version__)"` reports expected version.
- A known example runs serially and, if needed, under MPI.
- For source builds, `make check` passes (optionally with explicit `RUNCODE`) (`doc/docs/Build_From_Source.md`).
- If build behavior is unclear, inspect `src/Makefile.am`, `python/Makefile.am`, `scheme/Makefile.am`, `src/support/Makefile.am`, and `python/meep.i`.

## Scope
- Handle questions about build, installation, compilation, and environment setup.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `doc/docs/Installation.md`
- `doc/docs/FAQ.md`
- `doc/docs/Build_From_Source.md`
- `doc/docs/Scheme_Tutorials/Local_Density_of_States.md`
- `doc/docs/Scheme_Tutorials/Third_Harmonic_Generation.md`
- `doc/docs/Scheme_Tutorials/Resonant_Modes_and_Transmission_in_a_Waveguide_Cavity.md`
- `doc/docs/Scheme_Tutorials/Optical_Forces.md`
- `doc/docs/Scheme_Tutorials/Cylindrical_Coordinates.md`
- `doc/docs/Download.md`
- `doc/docs/Scheme_Tutorials/Multilevel_Atomic_Susceptibility.md`

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
- `src/Makefile.am` | core `libmeep` targets and dependency linkage
- `src/support/Makefile.am` | support-library objects required at link time
- `python/Makefile.am` | Python module build/install targets and SWIG outputs
- `libpympb/Makefile.am` | MPB linkage used by mode/eigensolver features
- `scheme/Makefile.am` | Scheme binding build/install targets
- `python/meep.i` | SWIG interface declarations for Python bindings
- `python/meep-python.hpp` | wrapper helper types used by the Python extension
- `python/typemap_utils.cpp` | Python<->C++ conversion helpers for bindings
- `scheme/meep.cpp` | Scheme/Guile module entry points
- `tests/Makefile.am` | `make check` target wiring
- `python/tests/test_simulation.py` | post-install Python runtime smoke check
- `tests/known_results.cpp` | C++ baseline regression entry point
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" libpympb python scheme src`).
