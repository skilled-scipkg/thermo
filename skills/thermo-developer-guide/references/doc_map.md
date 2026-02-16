# thermo documentation map: Developer Guide

Use docs first, then move to `references/source_map.md` for function-level extension/debugging.

## Start docs for estimator internals
1. `docs/thermo.group_contribution.wilson_jasperson.rst` - Tc/Pc estimates and increment caveats.
2. `docs/thermo.group_contribution.joback.rst` - Joback fragmentation/estimation APIs.
3. `docs/thermo.group_contribution.fedors.rst` - Fedors Vc estimates and diagnostic flags.
4. `docs/thermo.group_contribution.bondi.rst` - Bondi R/Q and van der Waals estimates.

## Validation docs/tests to pair with startup
- `tests/test_wilson_jasperson.py`
- `tests/test_joback.py`
- `tests/test_fedors.py`
- `tests/test_bondi.py`

## File inventory
- `docs/thermo.group_contribution.wilson_jasperson.rst` | title: Wilson-Jasperson Group Contribution Method (thermo.group_contribution.wilson_jasperson)
- `docs/thermo.group_contribution.joback.rst` | title: Joback Group Contribution Method (thermo.group_contribution.joback)
- `docs/thermo.group_contribution.fedors.rst` | title: Fedors Group Contribution Method (thermo.group_contribution.fedors)
- `docs/thermo.group_contribution.bondi.rst` | title: Bondi Group Contribution Method (thermo.group_contribution.bondi)
