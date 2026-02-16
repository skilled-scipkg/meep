---
name: meep-index
description: This skill should be used when users ask how to use meep and the correct generated documentation skill must be selected before going deeper into source code.
---

# meep Skills Index

## Route the request
- Classify the request into one of the generated topic skills listed below.
- Prefer abstract, workflow-level guidance for large scientific packages; do not attempt full function-by-function coverage unless explicitly requested.

## Generated topic skills
- `meep-inputs-and-modeling`: Inputs and Modeling (inputs, system setup, models, and physical parameterization)
- `meep-build-and-install`: Build and Install (build, installation, compilation, and environment setup)
- `meep-simulation-workflows`: Simulation Workflows (simulation setup, execution flow, and runtime controls)
- `meep-api-and-scripting`: API and Scripting (language bindings, APIs, and programmatic interfaces)
- `meep-getting-started`: Getting Started (initial setup, quickstarts, and core concepts)
- `meep-examples-and-tutorials`: Examples and Tutorials (worked examples, tutorials, and cookbook usage)
- `meep-advanced-topics`: Advanced Topics (consolidated one-doc advanced references: symmetry, PML, Yee lattice, units/nonlinearity, special-kz, eigensolver math, Scheme info, legal/credits)

## Documentation-first inputs
- `doc/bfast`
- `doc/docs`

## Tutorials and examples roots
- `python/examples`
- `scheme/examples`
- `doc/docs/Python_Tutorials`
- `doc/docs/Scheme_Tutorials`

## Test roots for behavior checks
- `tests`
- `python/tests`

## Start from `skills/`
- If the current working directory is `skills/`, run `cd ..` before opening docs/examples.
- If the current working directory is a topic folder such as `skills/meep-getting-started`, run `cd ../..` first.
- Quick environment smoke check: `python -c "import meep as mp; print(mp.__version__)"`

## Escalate only when needed
- Start from topic skill primary references.
- If those references are insufficient, open that skill's doc map (for example: `skills/meep-getting-started/references/doc_map.md`).
- If documentation still leaves ambiguity, open that skill's source map (for example: `skills/meep-getting-started/references/source_map.md`) and inspect the suggested source entry points.
- Use targeted symbol search while inspecting source (e.g., `rg -n "<symbol_or_keyword>" libpympb python scheme src`).

## Source directories for deeper inspection
- `libpympb`
- `python`
- `scheme`
- `src`
