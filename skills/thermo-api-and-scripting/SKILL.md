---
name: thermo-api-and-scripting
description: This skill should be used when users ask about api and scripting in thermo; it prioritizes documentation references and then source inspection only for unresolved details.
---

# thermo: API and Scripting

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## High-Signal Playbook
### Route conditions
- Route to `thermo-getting-started` when the user needs end-to-end flash setup instead of API mechanics (`docs/tutorial_phases_and_flash.rst`).
- Route to `thermo-inputs-and-modeling` for activity-model selection and parameterization workflows (`docs/activity_coefficients.rst`, `docs/thermo.unifac.rst`).
- Route to `thermo-theory-and-methods` for cubic EOS derivations and solver internals (`docs/cubic_equations_of_state.rst`, `docs/thermo.eos_volume.rst`).
- Route to `thermo-advanced-topics` for module-specific APIs outside this cluster (transport/legacy modules).

### Triage questions
- Is the target API a `TDependentProperty`, `TPDependentProperty`, or `MixtureProperty` pattern?
- Should methods be auto-selected or explicitly pinned?
- Is out-of-range extrapolation acceptable?
- Is RDKit available for functional-group APIs?
- Is vectorized EOS-alpha behavior needed, or class-based EOS objects are sufficient?
- Is reproducibility/caching behavior required (JSON/hash, stable method selection)?

### Canonical workflow
1. Instantiate the property/API object with the minimum required constants and identifiers (`docs/property_objects.rst`, `docs/thermo.utils.rst`).
2. Inspect `method`, `all_methods`, and validity limits before bulk evaluation (`docs/property_objects.rst`).
3. Pin method/extrapolation only when needed and compute values via object call or explicit `calculate` path (`docs/property_objects.rst`).
4. Add derivative/integral checks if the API is used inside optimizers/regressions (`docs/property_objects.rst`).
5. For structure-based APIs, run functional-group checks with RDKit-backed helpers (`docs/thermo.functional_groups.rst`, `thermo/functional_groups.py`).
6. For EOS-alpha vectorized routines, verify outputs against class-based EOS behavior before optimization (`docs/thermo.eos_alpha_functions.rst`).

### Quick simulation start
```bash
python - <<'PY'
from thermo import VaporPressure
from thermo.utils.functional import identify_phase

vp = VaporPressure(Tb=351.39, Tc=514.0, Pc=6137000.0, omega=0.635, CASRN='64-17-5')
print(vp.method, round(vp(300.0), 3))
print(sorted(vp.valid_methods(300.0))[:3])
print(identify_phase(T=300.0, P=101325.0, Tb=351.39, Tc=514.0, Psat=vp(300.0)))
PY
pytest -q tests/test_t_dependent_property.py tests/test_tp_dependent_property.py tests/test_interface.py
```

Validation checkpoints:
- `VaporPressure` call path and pinned-method path return finite values.
- Property-object/interface test subset passes before API refactors.

### Minimal working example
```python
from thermo import VaporPressure

ethanol_psat = VaporPressure(
    Tb=351.39, Tc=514.0, Pc=6137000.0, omega=0.635, CASRN='64-17-5'
)
print(ethanol_psat.method)
print(ethanol_psat(300.0))
print(ethanol_psat.valid_methods(100.0))

ethanol_psat.method = 'ANTOINE_POLING'
print(ethanol_psat.calculate(T=300.0, method='ANTOINE_POLING'))
```
Source: `docs/property_objects.rst`.

### Pitfalls/fixes
- Forcing a method that is not available raises exceptions; check `valid_methods` first (`docs/property_objects.rst`).
- `load_data=False` can leave `method=None`; provide coefficients/constants explicitly in that mode (`docs/property_objects.rst`).
- Extrapolated values can be physically poor outside method ranges; inspect `T_limits` before trusting outputs (`docs/property_objects.rst`).
- Functional-group APIs require RDKit; missing RDKit should be handled as an environment issue (`docs/thermo.functional_groups.rst`, `thermo/functional_groups.py`).
- `thermo.eos_mix_methods` is documented as overflow/internal style helpers, not first-line user APIs (`docs/thermo.eos_mix_methods.rst`).

### Convergence/validation checks
- Compare `obj(T)` with `obj.calculate(T, method=obj.method)` on representative points (`docs/property_objects.rst`).
- Confirm selected methods remain valid over the full operating range using `valid_methods(T)` (`docs/property_objects.rst`).
- Validate derivative/integral outputs against finite-difference sanity checks for optimization workflows.
- For alpha-function workflows, cross-check at least one state against EOS class output (`docs/thermo.eos_alpha_functions.rst`, `thermo/eos.py`, `thermo/eos_mix.py`).

## Scope
- Handle questions about language bindings, APIs, and programmatic interfaces.
- Keep responses API-surface and scripting focused unless the user asks for theoretical derivation.

## Primary documentation references
- `docs/thermo.utils.rst`
- `docs/thermo.interface.rst`
- `docs/thermo.functional_groups.rst`
- `docs/thermo.eos_alpha_functions.rst`
- `docs/property_objects.rst`

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
- `thermo/utils/t_dependent_property.py`
- `thermo/utils/tp_dependent_property.py`
- `thermo/utils/mixture_property.py`
- `thermo/utils/functional.py`
- `thermo/interface.py`
- `thermo/functional_groups.py`
- `thermo/eos_alpha_functions.py`
- `thermo/eos.py`
- `thermo/eos_mix.py`
- `thermo/phases/phase_utils.py`
- `thermo/flash/flash_utils.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" thermo`).
