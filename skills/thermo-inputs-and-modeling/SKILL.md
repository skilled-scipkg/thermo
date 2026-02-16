---
name: thermo-inputs-and-modeling
description: This skill should be used when users ask about inputs and modeling in thermo; it prioritizes documentation references and then source inspection only for unresolved details.
---

# thermo: Inputs and Modeling

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## High-Signal Playbook
### Route conditions
- Route to `thermo-getting-started` for first flash setup or package bootstrapping questions (`docs/tutorial_phases_and_flash.rst`, `docs/chemical_package_tutorial.rst`).
- Route to `thermo-theory-and-methods` for EOS derivation/root-solver math and cubic formalism questions (`docs/cubic_equations_of_state.rst`, `docs/thermo.eos_volume.rst`).
- Route to `thermo-api-and-scripting` for property-method dispatch/extrapolation behavior (`docs/property_objects.rst`, `docs/thermo.utils.rst`).
- Route to `thermo-advanced-topics` for module-specific legacy APIs (for example `thermo.property_package`, `thermo.mixture`) when no model-selection decision is needed.

### Triage questions
- Is the user choosing between predictive and regressed activity models?
- Do they already have interaction parameters (`NRTL/Wilson/UNIQUAC`) or only structures/subgroups (`UNIFAC`)?
- Is temperature dependence required, or only composition dependence at fixed `T`?
- Are they building a standalone Gibbs-excess model, or embedding it in `GibbsExcessLiquid`/EOS workflows?
- Do they need high-component-count performance (numba, reuse with `to_T_xs`)?
- Are units/phase-basis assumptions already fixed?

### Canonical workflow
1. Select the model family from available data: regressed (`NRTL`, `Wilson`, `UNIQUAC`) vs predictive (`UNIFAC`, `RegularSolution`) (`docs/activity_coefficients.rst`).
2. Construct the Gibbs-excess object with explicit `T`, composition, and model parameters/subgroups (`docs/activity_coefficients.rst`, `docs/thermo.unifac.rst`).
3. Evaluate primary outputs (`gammas`, `GE`, derivatives) for the target state (`docs/activity_coefficients.rst`).
4. Reuse via `to_T_xs` for nearby states to avoid expensive full reinitialization (`docs/activity_coefficients.rst`).
5. Embed the model into a phase/EOS workflow only after local model checks pass (`docs/activity_coefficients.rst`, `docs/thermo.phases.rst`).
6. If defaults in GibbsExcessLiquid look surprising, explicitly set needed options and verify behavior (`docs/GibbsExcessLiquid_details.rst`).

### Quick simulation start
```bash
python - <<'PY'
from thermo.unifac import UFIP, UFSG, UNIFAC

GE = UNIFAC.from_subgroups(
    chemgroups=[{1: 2, 2: 4}, {1: 1, 2: 1, 18: 1}],
    T=333.15,
    xs=[0.5, 0.5],
    version=0,
    interaction_data=UFIP,
    subgroups=UFSG,
)
print([round(v, 6) for v in GE.gammas()])
print(round(GE.to_T_xs(T=353.15, xs=[0.3, 0.7]).GE(), 6))
PY
pytest -q tests/test_unifac.py tests/test_nrtl.py tests/test_wilson.py
```

Validation checkpoints:
- `gammas` are finite and physically reasonable for both states.
- Regression-model tests pass before tuning parameters.

### Minimal working example
```python
from thermo.unifac import UFIP, UFSG, UNIFAC

GE = UNIFAC.from_subgroups(
    chemgroups=[{1: 2, 2: 4}, {1: 1, 2: 1, 18: 1}],
    T=60 + 273.15,
    xs=[0.5, 0.5],
    version=0,
    interaction_data=UFIP,
    subgroups=UFSG,
)
print(GE.gammas())
print(GE.to_T_xs(T=400.0, xs=[0.1, 0.9]).gammas())
```
Source: `docs/activity_coefficients.rst`.

### Pitfalls/fixes
- `NRTL`, `Wilson`, and `UNIQUAC` are regression-based; missing/poor parameters dominate errors (`docs/activity_coefficients.rst`).
- `version` and subgroup numbering are easy to mix up across UNIFAC variants; keep data/version aligned (`docs/activity_coefficients.rst`).
- First-time model setup can be expensive; reuse with `to_T_xs` for sweeps (`docs/activity_coefficients.rst`).
- Numpy inputs can be slower in CPython unless using the numba path for large systems (`docs/activity_coefficients.rst`).
- GibbsExcessLiquid has many "ideal" options and defaults can surprise; do not assume default semantics (`docs/GibbsExcessLiquid_details.rst`).
- Treat phase/equilibrium layers separately from excess-model layer to isolate errors (`docs/activity_coefficients.rst`).

### Convergence/validation checks
- Reproduce a published benchmark (for example the DDBST UNIFAC example) before large studies (`docs/activity_coefficients.rst`).
- Check `gammas` and `GE` trends under small `T`/`x` perturbations using `to_T_xs` (`docs/activity_coefficients.rst`).
- Validate that mole fractions are normalized and model outputs remain finite over the intended domain.
- For fitted models, compare at least one independent data point beyond fit anchors.

## Scope
- Handle questions about inputs, system setup, models, and physical parameterization.
- Keep responses architectural and data-dependency focused unless the user requests full API detail.

## Primary documentation references
- `docs/thermo.wilson.rst`
- `docs/thermo.uniquac.rst`
- `docs/thermo.unifac.rst`
- `docs/thermo.regular_solution.rst`
- `docs/thermo.phases.rst`
- `docs/thermo.nrtl.rst`
- `docs/GibbsExcessLiquid_details.rst`
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
- `thermo/wilson.py`
- `thermo/uniquac.py`
- `thermo/unifac.py`
- `thermo/nrtl.py`
- `thermo/regular_solution.py`
- `thermo/activity.py`
- `thermo/phases/gibbs_excess.py`
- `thermo/phases/phase_utils.py`
- `thermo/phases/ceos.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" thermo`).
