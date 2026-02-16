# thermo source map: Inputs and Modeling

Use this map after checking `references/doc_map.md`.

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## Fast source navigation
- `rg -n "class (NRTL|Wilson|UNIQUAC|UNIFAC|RegularSolution)|to_T_xs" thermo`
- `rg -n "GibbsExcess|gammas\(|GE\(" thermo/activity.py thermo/phases/gibbs_excess.py`

## Function-level source entry points
- `thermo/activity.py` | symbols: `GibbsExcess`, `gibbs_excess_gammas`, `interaction_exp` | behavior: shared Gibbs-excess base/derivative logic.
- `thermo/nrtl.py` | symbols: `NRTL`, `nrtl_gammas`, `nrtl_GE` | behavior: regressed-NRTL activity calculations.
- `thermo/wilson.py` | symbols: `Wilson`, `wilson_gammas`, `Wilson_gammas` | behavior: Wilson model formulas and wrappers.
- `thermo/uniquac.py` | symbols: `UNIQUAC`, `uniquac_gammas_from_args`, `uniquac_GE` | behavior: UNIQUAC residual/combinatorial terms.
- `thermo/unifac.py` | symbols: `UNIFAC`, `UNIFAC_gammas`, `unifac_gammas` | behavior: predictive subgroup model and caches.
- `thermo/regular_solution.py` | symbols: `RegularSolution`, `regular_solution_gammas`, `FloryHuggins` | behavior: regular-solution family and derivatives.
- `thermo/phases/gibbs_excess.py` | symbols: `GibbsExcessLiquid` | behavior: embedding excess models into phase calculations.

## Simulation-ready checks
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
pytest -q tests/test_unifac.py tests/test_nrtl.py tests/test_wilson.py tests/test_uniquac.py tests/test_regular_solution.py
```

## Validation checkpoints
- `gammas` and `GE` are finite for base and perturbed states.
- Core model tests pass before parameter tuning or model substitutions.
