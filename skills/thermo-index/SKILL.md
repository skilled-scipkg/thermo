---
name: thermo-index
description: This skill should be used when users ask how to use thermo and the correct generated documentation skill must be selected before going deeper into source code.
---

# thermo Skills Index

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## Route the request
- Classify the request into one of the topic skills below before answering in depth.
- Keep responses workflow-level first; only drill into source after docs and topic maps are exhausted.

## Generated topic skills
- `thermo-getting-started`: first workflows, flash setup, constants/correlations packages, reproducible initialization
- `thermo-inputs-and-modeling`: model and parameter choices (activity models, phases, Gibbs-excess configuration)
- `thermo-theory-and-methods`: cubic EOS formalism, solver behavior, derivatives, method tradeoffs
- `thermo-api-and-scripting`: programmatic APIs for properties/utilities/functional groups/alpha functions
- `thermo-developer-guide`: extending and validating group-contribution internals
- `thermo-advanced-topics`: consolidated advanced/legacy modules, install/docs index, notebook catalog, and specialized APIs

## Documentation-first inputs
- `docs`

## Tutorials and examples roots
- `docs/Examples`
- `docs/plots`

## Test roots for behavior checks
- `tests`

## Escalate only when needed
- Start with topic skill primary references.
- If those references are insufficient, inspect the topic skill `references/doc_map.md`.
- If ambiguity remains, inspect that topic skill `references/source_map.md` and follow ranked entry points.
- Use targeted symbol search while inspecting source (for example: `rg -n "<symbol_or_keyword>" thermo`).

## Source directories for deeper inspection
- `thermo`
