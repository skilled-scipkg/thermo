# thermo source map: Getting Started

Use this map after checking `references/doc_map.md`.

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## Fast source navigation
- `rg -n "FlashPureVLS|FlashVL|ChemicalConstantsPackage|PropertyCorrelationsPackage|CEOS" thermo`
- `rg -n "Phase\.to|to_TP_zs|flash\(" thermo/phases thermo/flash`

## Function-level source entry points
- `thermo/chemical_package.py` | symbols: `ChemicalConstantsPackage`, `PropertyCorrelationsPackage`, `_from_IDs` | behavior: constant/correlation package construction and defaults.
- `thermo/phases/ceos.py` | symbols: `CEOSPhase`, `CEOSGas`, `CEOSLiquid` | behavior: phase-model implementations used in startup flash flows.
- `thermo/flash/flash_pure_vls.py` | symbols: `FlashPureVLS` | behavior: pure-component flash entry class.
- `thermo/flash/flash_vl.py` | symbols: `FlashVL` | behavior: generic VL flash path when users graduate from pure systems.
- `thermo/flash/flash_utils.py` | symbols: `TVF_pure_newton`, `PVF_pure_newton`, `sequential_substitution_2P` | behavior: lower-level solver routines when convergence is unstable.
- `thermo/phases/phase.py` | symbols: `Phase`, `Phase.to`, `Phase.to_TP_zs` | behavior: efficient state updates for sweeps.
- `thermo/property_package.py` | symbols: `PropertyPackage`, `StabilityTester`, `Rachford_Rice_solution_negative` | behavior: legacy package and phase-split checks.

## Simulation-ready checks
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
res = FlashPureVLS(
    constants,
    correlations,
    gas=CEOSGas(PRMIX, eos_kwargs=eos_kwargs),
    liquids=[CEOSLiquid(PRMIX, eos_kwargs=eos_kwargs)],
    solids=[],
).flash(T=320.0, P=2e5)
print(res.phase, round(res.T, 3), round(res.P, 1))
PY
pytest -q tests/test_flash_pure.py tests/test_flash_vl.py
```

## Validation checkpoints
- Flash result has a valid phase label and finite thermodynamic state.
- `test_flash_pure.py` and `test_flash_vl.py` pass before changing solver logic.
