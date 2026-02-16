# thermo source map: Developer Guide

Use this map after checking `references/doc_map.md`.

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```
RDKit is required for this topic.

## Fast source navigation
- `rg -n "class (Joback|DikyJoback|BondiGroupContribution|BaseGroupContribution)|Wilson_Jasperson|Fedors" thermo/group_contribution`
- `rg -n "smarts_fragment|load_rdkit_modules|identify_functional_groups" thermo/group_contribution thermo/functional_groups.py`

## Function-level source entry points
- `thermo/group_contribution/group_contribution_base.py` | symbols: `BaseGroupContribution`, `smarts_fragment_priority`, `smarts_fragment` | behavior: shared SMARTS fragmentation engine.
- `thermo/group_contribution/joback.py` | symbols: `Joback`, `JobackGroupContribution`, `load_rdkit_modules` | behavior: Joback estimates and fragment assignment.
- `thermo/group_contribution/wilson_jasperson.py` | symbols: `Wilson_Jasperson` | behavior: Tc/Pc estimation with increment flags.
- `thermo/group_contribution/fedors.py` | symbols: `Fedors` | behavior: Vc estimate with status/unknown diagnostics.
- `thermo/group_contribution/bondi.py` | symbols: `bondi_fragmentation`, `bondi_van_der_waals_surface_area_volume`, `R_Q_from_bondi` | behavior: Bondi-derived R/Q and molecular descriptors.
- `thermo/functional_groups.py` | symbols: `identify_functional_groups`, `identify_functional_group_atoms` | behavior: fallback checks for failed fragmentations.

## Simulation-ready checks
```bash
python - <<'PY'
from thermo.group_contribution import Wilson_Jasperson
from thermo.group_contribution.fedors import Fedors

Tc, Pc, miss_Tc, miss_Pc = Wilson_Jasperson('CCC1=CC=CC=C1O', Tb=477.67)
print(round(Tc, 3), round(Pc, 3), miss_Tc, miss_Pc)

Vc, status, unknown_atoms, unknown_bonds, unknown_rings = Fedors('CCC(C)O')
print(round(Vc, 8), status, unknown_atoms, unknown_bonds, unknown_rings)
PY
pytest -q tests/test_wilson_jasperson.py tests/test_fedors.py tests/test_joback.py tests/test_bondi.py
```

## Validation checkpoints
- Estimator outputs are finite and diagnostic flags are captured.
- Group-contribution test subset passes before modifying SMARTS catalogs.
- RDKit must be importable for these paths.
