# thermo documentation map: Inputs and Modeling

Use docs first, then move to `references/source_map.md` for symbol-level behavior checks.

## Start docs for model setup
1. `docs/activity_coefficients.rst` - model-selection workflow and performance guidance.
2. `docs/thermo.nrtl.rst` - NRTL equations and API.
3. `docs/thermo.wilson.rst` - Wilson equations and API.
4. `docs/thermo.uniquac.rst` - UNIQUAC equations and API.
5. `docs/thermo.unifac.rst` - UNIFAC subgroup/data workflows.
6. `docs/thermo.regular_solution.rst` - RegularSolution/Flory-Huggins/Hansen models.
7. `docs/thermo.phases.rst` - embedding models into phase objects.
8. `docs/GibbsExcessLiquid_details.rst` - GibbsExcessLiquid defaults and edge options.

## Validation docs/tests to pair with startup
- `tests/test_unifac.py`
- `tests/test_nrtl.py`
- `tests/test_wilson.py`
- `tests/test_uniquac.py`
- `tests/test_regular_solution.py`

## File inventory
- `docs/activity_coefficients.rst` | title: Introduction to Activity Coefficient Models
- `docs/thermo.nrtl.rst` | title: NRTL Gibbs Excess Model (thermo.nrtl)
- `docs/thermo.wilson.rst` | title: Wilson Gibbs Excess Model (thermo.wilson)
- `docs/thermo.uniquac.rst` | title: UNIQUAC Gibbs Excess Model (thermo.uniquac)
- `docs/thermo.unifac.rst` | title: UNIFAC Gibbs Excess Model (thermo.unifac)
- `docs/thermo.regular_solution.rst` | title: Regular Solution Gibbs Excess Model (thermo.regular_solution)
- `docs/thermo.phases.rst` | title: Phase Models (thermo.phases)
- `docs/GibbsExcessLiquid_details.rst` | title: Details of GibbsExcessLiquid Phase Model
