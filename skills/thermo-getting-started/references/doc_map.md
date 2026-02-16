# thermo documentation map: Getting Started

Use docs first, then move to `references/source_map.md` only when behavior is unclear.

## Start docs for first simulation
1. `docs/tutorial_phases_and_flash.rst` - end-to-end phase and flash setup.
2. `docs/chemical_package_tutorial.rst` - constants/correlations package construction.
3. `docs/property_objects.rst` - reusable property object patterns.
4. `docs/cubic_equations_of_state.rst` - EOS assumptions and validation identities.
5. `docs/activity_coefficients.rst` - when non-ideal liquid modeling is needed.

## Validation docs/tests to pair with startup
- `tests/test_flash_pure.py`
- `tests/test_flash_vl.py`
- `tests/test_chemical_package.py`
- `tests/test_phases.py`

## File inventory
- `docs/tutorial_phases_and_flash.rst` | title: Introduction to Phase and Flash Calculations
- `docs/chemical_package_tutorial.rst` | title: Introduction to ChemicalConstantsPackage and PropertyCorrelationsPackage
- `docs/property_objects.rst` | title: Introduction to Property Objects
- `docs/cubic_equations_of_state.rst` | title: Introduction to Cubic Equations of State
- `docs/activity_coefficients.rst` | title: Introduction to Activity Coefficient Models
