# thermo source map: API and Scripting

Use this map after checking `references/doc_map.md`.

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## Fast source navigation
- `rg -n "class (TDependentProperty|TPDependentProperty|MixtureProperty|SurfaceTension)" thermo/utils thermo/interface.py`
- `rg -n "identify_phase|assert_component_balance|TPD|PR_a_alphas_vectorized" thermo`

## Function-level source entry points
- `thermo/utils/t_dependent_property.py` | symbols: `TDependentProperty`, `create_local_method`, `load_json_based_correlations` | behavior: method selection, data loading, extrapolation base.
- `thermo/utils/tp_dependent_property.py` | symbols: `TPDependentProperty` | behavior: pressure-dependent extension of T-dependent properties.
- `thermo/utils/mixture_property.py` | symbols: `MixtureProperty` | behavior: mixture property API patterns and mixing logic.
- `thermo/interface.py` | symbols: `SurfaceTension`, `SurfaceTensionMixture` | behavior: concrete property class implementation.
- `thermo/utils/functional.py` | symbols: `identify_phase`, `TPD`, `assert_component_balance`, `assert_energy_balance` | behavior: scripting helpers and sanity checks.
- `thermo/functional_groups.py` | symbols: `load_rdkit_modules`, `identify_functional_groups`, `identify_functional_group_atoms` | behavior: RDKit-backed structure APIs.
- `thermo/eos_alpha_functions.py` | symbols: `PR_a_alphas_vectorized`, `SRK_a_alpha_and_derivatives_vectorized` | behavior: vectorized kernels for EOS alpha calculations.

## Simulation-ready checks
```bash
python - <<'PY'
from thermo import VaporPressure
from thermo.utils.functional import identify_phase

vp = VaporPressure(Tb=351.39, Tc=514.0, Pc=6137000.0, omega=0.635, CASRN='64-17-5')
print(vp.method, round(vp(300.0), 3))
print(sorted(vp.valid_methods(300.0))[:3])
print(identify_phase(T=300.0, P=101325.0, Tb=351.39, Tc=514.0, Psat=vp(300.0)))
PY
pytest -q tests/test_t_dependent_property.py tests/test_tp_dependent_property.py tests/test_interface.py tests/test_utils.py
```

## Validation checkpoints
- `obj(T)` and method-dispatch outputs are finite and consistent.
- Property/base-class tests pass before API behavior changes.
- If RDKit-backed APIs are modified, also run `pytest -q tests/test_functional_groups.py`.
