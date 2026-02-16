---
name: thermo-advanced-topics
description: This skill should be used when users ask about advanced and specialized topics in thermo; it prioritizes documentation references and then source inspection only for unresolved details.
---

# thermo: Advanced and Specialized Topics

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## High-Signal Playbook
### Route conditions
- Route to `thermo-getting-started` for first flash/package setup and beginner workflows (`docs/tutorial_phases_and_flash.rst`, `docs/chemical_package_tutorial.rst`).
- Route to `thermo-inputs-and-modeling` for activity/EOS model choice and parameterization (`docs/activity_coefficients.rst`, `docs/thermo.phases.rst`).
- Route to `thermo-theory-and-methods` for cubic-EOS derivations and root-solver internals (`docs/cubic_equations_of_state.rst`).
- Route to `thermo-api-and-scripting` for method dispatch/extrapolation/property-object scripting (`docs/property_objects.rst`, `docs/thermo.utils.rst`).

### Triage questions
- Which specific module/page is being asked about (for example `thermo.volume`, `thermo.viscosity`, `thermo.stream`)?
- Is the request legacy API maintenance (`thermo.chemical`, `thermo.mixture`, `thermo.property_package`) or new workflows?
- Is units handling (`thermo.units`) part of the issue?
- Is this a doc lookup task or behavior-debug task requiring source/tests?
- Does the user need notebook examples or low-level API signatures?
- Are transport/property method limits/extrapolation concerns part of the ask?

### Canonical workflow
1. Identify the target advanced subtopic from the collapsed routing list below.
2. Start with the exact module doc page in `references/doc_map.md` (API signature and object surface).
3. If usage pattern is unclear, cross-check `docs/examples.rst` notebooks or related tests.
4. If behavior is still ambiguous, jump to the mapped source file in `references/source_map.md`.
5. Confirm units/basis assumptions before returning numerical guidance (`docs/thermo.units.rst`).
6. Route out to a core skill if the ask expands into setup/modeling/theory.

### Quick simulation start
```bash
python - <<'PY'
from thermo import Chemical, Stream

ethanol = Chemical('ethanol', T=300.0, P=101325.0)
print(round(ethanol.Psat, 3), round(ethanol.rhog, 6))

s = Stream(['water', 'ethanol'], zs=[0.6, 0.4], T=350.0, P=101325.0, m=1.0)
print(s.phase, round(s.H(), 3), round(s.Cp(), 3))
PY
pytest -q tests/test_stream.py tests/test_units.py tests/test_chemical.py
```

Validation checkpoints:
- Property calls return finite values in consistent units.
- Stream/units/chemical tests pass before debugging deeper modules.

### Minimal working example
```python
from thermo.units import *

kwargs = dict(
    T=400.0*u.degC,
    P=30*u.psi,
    Tcs=[126.1, 190.6]*u.K,
    Pcs=[33.94E5, 46.04E5]*u.Pa,
    omegas=[0.04, 0.011]*u.dimensionless,
    zs=[0.5, 0.5]*u.dimensionless,
    kijs=[[0.0, 0.0289], [0.0289, 0.0]]*u.dimensionless,
)
eos_units = PRMIX(**kwargs)
print(eos_units.H_dep_g, eos_units.T)
```
Source: `docs/thermo.units.rst`.

### Pitfalls/fixes
- Many advanced pages are automodule/API references with little narrative; jump to source/tests quickly when behavior questions appear.
- Legacy modules (`thermo.chemical`, `thermo.mixture`, `thermo.property_package`) can differ from newer phase/flash workflows; clarify intent before advising.
- Units wrapper requires consistent `pint` quantities across inputs (`docs/thermo.units.rst`, `docs/index.rst`).
- Example notebooks may depend on companion packages (`fluids`, `ht`, `chemicals`, `pint`) (`docs/examples.rst`).
- Property/transport methods can fail outside validity ranges; inspect method limits and extrapolation behavior (`docs/property_objects.rst`).

### Convergence/validation checks
- Confirm class/function exists in the cited module before giving procedural guidance.
- For numerical outputs, verify unit basis (molar vs mass) and dimensional consistency.
- For legacy workflow questions, cross-check one result against a core-skill workflow where possible.
- Validate behavior using at least one relevant test path when docs are sparse.

## Collapsed one-doc topic routes
- Installation and docs index: `docs/index.rst`
- Examples notebook catalog: `docs/examples.rst`
- API modules: `docs/thermo.activity.rst`, `docs/thermo.bulk.rst`, `docs/thermo.chemical.rst`, `docs/thermo.chemical_package.rst`, `docs/thermo.datasheet.rst`, `docs/thermo.electrochem.rst`, `docs/thermo.equilibrium.rst`, `docs/thermo.flash.rst`, `docs/thermo.heat_capacity.rst`, `docs/thermo.interaction_parameters.rst`, `docs/thermo.law.rst`, `docs/thermo.mixture.rst`, `docs/thermo.permittivity.rst`, `docs/thermo.phase_change.rst`, `docs/thermo.phase_identification.rst`, `docs/thermo.property_package.rst`, `docs/thermo.stream.rst`, `docs/thermo.thermal_conductivity.rst`, `docs/thermo.units.rst`, `docs/thermo.vapor_pressure.rst`, `docs/thermo.viscosity.rst`, `docs/thermo.volume.rst`

## Scope
- Handle specialized/overflow topics not covered by the core skills.
- Keep answers compact and module-targeted; route out quickly when the request broadens.

## Primary documentation references
- `docs/index.rst`
- `docs/examples.rst`
- `docs/thermo.activity.rst`
- `docs/thermo.bulk.rst`
- `docs/thermo.chemical.rst`
- `docs/thermo.chemical_package.rst`
- `docs/thermo.datasheet.rst`
- `docs/thermo.electrochem.rst`
- `docs/thermo.equilibrium.rst`
- `docs/thermo.flash.rst`
- `docs/thermo.heat_capacity.rst`
- `docs/thermo.interaction_parameters.rst`
- `docs/thermo.law.rst`
- `docs/thermo.mixture.rst`
- `docs/thermo.permittivity.rst`
- `docs/thermo.phase_change.rst`
- `docs/thermo.phase_identification.rst`
- `docs/thermo.property_package.rst`
- `docs/thermo.stream.rst`
- `docs/thermo.thermal_conductivity.rst`
- `docs/thermo.units.rst`
- `docs/thermo.vapor_pressure.rst`
- `docs/thermo.viscosity.rst`
- `docs/thermo.volume.rst`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use tutorials/examples as executable usage patterns when available.
- Use tests as behavior or regression references when available.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the ranked source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `docs/Examples`
- `docs/plots`

## Test references
- `tests`

## Optional deeper inspection
- `thermo`

## Source entry points for unresolved issues
- `thermo/activity.py`
- `thermo/bulk.py`
- `thermo/chemical.py`
- `thermo/chemical_package.py`
- `thermo/datasheet.py`
- `thermo/electrochem.py`
- `thermo/equilibrium.py`
- `thermo/flash/flash_vl.py`
- `thermo/heat_capacity.py`
- `thermo/interaction_parameters.py`
- `thermo/law.py`
- `thermo/mixture.py`
- `thermo/permittivity.py`
- `thermo/phase_change.py`
- `thermo/phase_identification.py`
- `thermo/property_package.py`
- `thermo/stream.py`
- `thermo/thermal_conductivity.py`
- `thermo/units.py`
- `thermo/vapor_pressure.py`
- `thermo/viscosity.py`
- `thermo/volume.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" thermo`).
