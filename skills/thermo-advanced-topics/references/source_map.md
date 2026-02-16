# thermo source map: Advanced and Specialized Topics

Use this map after checking `references/doc_map.md`.

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## Fast source navigation
- `rg -n "class (Chemical|Stream|EquilibriumState|VaporPressure|ViscosityLiquid|VolumeLiquid)|def (mole_balance|energy_balance|identify_sort_phases)" thermo`
- `rg -n "InteractionParameterDB|legal_status|Laliberte_|tabulate_" thermo`

## Function-level source entry points
- `thermo/chemical.py` | symbols: `Chemical`, `ChemicalConstants`, `lock_properties` | behavior: legacy single-chemical state and property access.
- `thermo/chemical_package.py` | symbols: `ChemicalConstantsPackage`, `PropertyCorrelationsPackage` | behavior: constants/correlations plumbing used across advanced modules.
- `thermo/equilibrium.py` | symbols: `EquilibriumState` | behavior: equilibrium result object and derived-property getters.
- `thermo/stream.py` | symbols: `Stream`, `EquilibriumStream`, `mole_balance`, `energy_balance` | behavior: process-stream calculations and balance utilities.
- `thermo/phase_identification.py` | symbols: `identify_sort_phases`, `score_phases_VL`, `vapor_score_PIP` | behavior: multiphase identification and sorting criteria.
- `thermo/vapor_pressure.py` | symbols: `VaporPressure`, `SublimationPressure` | behavior: pressure-property methods and ranges.
- `thermo/viscosity.py` | symbols: `ViscosityLiquid`, `ViscosityGas`, `determine_PPDS9_limits` | behavior: transport-property methods and validity limits.
- `thermo/volume.py` | symbols: `VolumeLiquid`, `VolumeGas`, `VolumeSolid`, `Tait_parameters_COSTALD` | behavior: density/volume methods for multiple phases.
- `thermo/thermal_conductivity.py` | symbols: `ThermalConductivityLiquid`, `ThermalConductivityGas`, `ThermalConductivityGasMixture` | behavior: thermal-conductivity APIs for pure and mixture states.
- `thermo/electrochem.py` | symbols: `Laliberte_density`, `Laliberte_viscosity`, `balance_ions`, `ionic_strength` | behavior: electrolyte property and ion-balance routines.
- `thermo/interaction_parameters.py` | symbols: `InteractionParameterDB`, `ScalarParameterDB`, `load_all_interaction_parameters` | behavior: parameter database loading and lookup.
- `thermo/law.py` | symbols: `legal_status`, `economic_status` | behavior: regulatory/economic metadata lookup.
- `thermo/datasheet.py` | symbols: `tabulate_liq`, `tabulate_gas`, `tabulate_constants`, `tabulate_streams` | behavior: tabular reporting utilities.
- `thermo/units.py` | symbols: `u` (pint registry), module-level wrappers generated via `wrap_numpydoc_obj` | behavior: unit-aware class/function wrappers.

## Simulation-ready checks
```bash
python - <<'PY'
from thermo import Chemical, Stream

ethanol = Chemical('ethanol', T=300.0, P=101325.0)
print(round(ethanol.Psat, 3), round(ethanol.rhog, 6))

s = Stream(['water', 'ethanol'], zs=[0.6, 0.4], T=350.0, P=101325.0, m=1.0)
print(s.phase, round(s.H(), 3), round(s.Cp(), 3))
PY
pytest -q tests/test_stream.py tests/test_units.py tests/test_chemical.py tests/test_phase_identification.py
```

## Validation checkpoints
- Property and stream outputs are finite and physically plausible.
- Stream/units/chemical/phase-identification tests pass before module-level changes.
