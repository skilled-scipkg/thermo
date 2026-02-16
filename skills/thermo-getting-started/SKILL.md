---
name: thermo-getting-started
description: This skill should be used when users ask about getting started in thermo; it prioritizes documentation references and then source inspection only for unresolved details.
---

# thermo: Getting Started

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## High-Signal Playbook
### Route conditions
- Route to `thermo-inputs-and-modeling` when the request is mainly model selection/parameterization (NRTL/Wilson/UNIQUAC/UNIFAC/phase model wiring) rather than first-use setup (`docs/activity_coefficients.rst`, `docs/thermo.phases.rst`).
- Route to `thermo-theory-and-methods` when the user asks for EOS derivations, cubic-root behavior, or solver internals (`docs/cubic_equations_of_state.rst`, `docs/thermo.eos_volume.rst`).
- Route to `thermo-api-and-scripting` when the request is method-selection/extrapolation scripting on property objects (`docs/property_objects.rst`, `docs/thermo.utils.rst`).
- Route to `thermo-advanced-topics` for legacy module-specific API pages or install/examples index-only asks (`docs/index.rst`, `docs/examples.rst`).

### Triage questions
- Is this a pure-component flash (`FlashPureVLS`) or a mixture workflow?
- Which state spec is given (`T/P`, `T/VF`, `P/H`, `P/S`, `T/H`, `V/U`)?
- Are constants/correlations explicitly supplied, or should IDs be used to build them first?
- Is deterministic/no-lookup behavior required (`skip_missing=True`, explicit constants)?
- Is a single phase already known, so direct phase objects are enough?
- Is reproducibility required (JSON/hash/model reuse)?

### Canonical workflow
1. Build `ChemicalConstantsPackage` with at least critical constants and identifiers used by the chosen workflow (`docs/chemical_package_tutorial.rst`).
2. Build `PropertyCorrelationsPackage` and prefer explicit objects + `skip_missing=True` when reproducibility matters (`docs/tutorial_phases_and_flash.rst`).
3. Instantiate phase models (e.g., `CEOSLiquid`/`CEOSGas`, `IAPWS95Liquid`/`IAPWS95Gas`) (`docs/tutorial_phases_and_flash.rst`).
4. Create a flash object (`FlashPureVLS` for pure workflows shown in docs) (`docs/tutorial_phases_and_flash.rst`).
5. Run the flash with a supported pair of specs (`T/P`, `T/VF`, `P/VF`, `P/H`, `P/S`, `T/H`, `V/U`) (`docs/tutorial_phases_and_flash.rst`).
6. Inspect `phase`, phase objects, and key properties; treat results as SI molar by default (`docs/cubic_equations_of_state.rst`).
7. For repeated nearby states, use `Phase.to` / `Phase.to_TP_zs` instead of rebuilding from scratch (`docs/tutorial_phases_and_flash.rst`).
8. Serialize/hash objects when caching or sharing states across processes (`docs/tutorial_phases_and_flash.rst`, `docs/cubic_equations_of_state.rst`).

### Quick simulation start
```bash
python - <<'PY'
from thermo import (
    ChemicalConstantsPackage, PropertyCorrelationsPackage,
    CEOSGas, CEOSLiquid, FlashPureVLS, PRMIX
)

constants = ChemicalConstantsPackage(
    Tcs=[507.6], Pcs=[3025000.0], omegas=[0.2975], MWs=[58.1222], CASs=['67-64-1']
)
correlations = PropertyCorrelationsPackage(constants, skip_missing=True)
eos_kwargs = dict(Tcs=constants.Tcs, Pcs=constants.Pcs, omegas=constants.omegas)
gas = CEOSGas(PRMIX, eos_kwargs=eos_kwargs)
liq = CEOSLiquid(PRMIX, eos_kwargs=eos_kwargs)
res = FlashPureVLS(constants, correlations, gas=gas, liquids=[liq], solids=[]).flash(T=320.0, P=2e5)
print(res.phase, round(res.T, 3), round(res.P, 1))
PY
pytest -q tests/test_flash_pure.py
```

Validation checkpoints:
- Printed state has a valid phase label and finite `T/P`.
- `tests/test_flash_pure.py` passes before deeper debugging.

### Minimal working example
```python
from thermo import (
    ChemicalConstantsPackage, PropertyCorrelationsPackage,
    HeatCapacityGas, CEOSLiquid, CEOSGas, FlashPureVLS, PRMIX
)
from thermo.heat_capacity import POLING_POLY

Cp = HeatCapacityGas(CASRN='67-56-1')
Cp.method = POLING_POLY
HeatCapacityGases = [Cp]

constants = ChemicalConstantsPackage(
    Tcs=[512.5], Pcs=[8084000.0], omegas=[0.559], MWs=[32.04186], CASs=['67-56-1']
)
correlations = PropertyCorrelationsPackage(constants, HeatCapacityGases=HeatCapacityGases, skip_missing=True)
eos_kwargs = dict(Tcs=constants.Tcs, Pcs=constants.Pcs, omegas=constants.omegas)
liquid = CEOSLiquid(PRMIX, HeatCapacityGases=HeatCapacityGases, eos_kwargs=eos_kwargs)
gas = CEOSGas(PRMIX, HeatCapacityGases=HeatCapacityGases, eos_kwargs=eos_kwargs)
flasher = FlashPureVLS(constants, correlations, gas=gas, liquids=[liquid], solids=[])
res = flasher.flash(T=300, P=1e5)
print(res.phase)
```
Source: `docs/tutorial_phases_and_flash.rst`.

### Pitfalls/fixes
- Flashes with two specs chosen only from `{H, S, U}` are not implemented; pair one of them with `T/P/V` (`docs/tutorial_phases_and_flash.rst`).
- Mixing inconsistent phase models can slow flashes and create discontinuities; prefer consistent gas/liquid model families first (`docs/tutorial_phases_and_flash.rst`).
- Treat `ChemicalConstantsPackage` as immutable; rebuild instead of mutating in place (`docs/chemical_package_tutorial.rst`).
- Blindly trusting external data can propagate bad values; validate and override suspect constants (`docs/chemical_package_tutorial.rst`).
- Expect floating-point noise in EOS identities; validate with tolerances, not exact equality (`docs/cubic_equations_of_state.rst`).
- If no methods load for a property object, check required constructor inputs or data loading settings (`docs/property_objects.rst`).

### Convergence/validation checks
- Confirm flash result physically: phase label/betas consistent with your spec (`docs/tutorial_phases_and_flash.rst`).
- Cross-check one state with an alternate spec route (for example `T/P` vs `T/VF`) when possible (`docs/tutorial_phases_and_flash.rst`).
- Verify EOS residual consistency by recomputing pressure from returned `V` (allowing float tolerance) (`docs/cubic_equations_of_state.rst`).
- Use `model_hash`/`state_hash` or JSON round-trip equality for reproducibility checks (`docs/tutorial_phases_and_flash.rst`, `docs/cubic_equations_of_state.rst`).

## Scope
- Handle questions about initial setup, quickstarts, and core concepts.
- Keep responses architectural and workflow-first unless the user asks for API-by-API detail.

## Primary documentation references
- `docs/tutorial_phases_and_flash.rst`
- `docs/cubic_equations_of_state.rst`
- `docs/chemical_package_tutorial.rst`
- `docs/property_objects.rst`
- `docs/activity_coefficients.rst`

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
- `thermo/flash/flash_pure_vls.py`
- `thermo/flash/flash_vl.py`
- `thermo/flash/flash_utils.py`
- `thermo/phases/ceos.py`
- `thermo/phases/gibbs_excess.py`
- `thermo/phases/phase.py`
- `thermo/property_package.py`
- `thermo/chemical_package.py`
- `thermo/chemical_utils.py`
- `thermo/utils/tp_dependent_property.py`
- `thermo/utils/t_dependent_property.py`
- `thermo/utils/mixture_property.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" thermo`).
