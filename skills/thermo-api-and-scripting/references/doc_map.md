# thermo documentation map: API and Scripting

Use docs first, then move to `references/source_map.md` for implementation-level checks.

## Start docs for API behavior
1. `docs/property_objects.rst` - `TDependentProperty`/`TPDependentProperty` usage and method dispatch.
2. `docs/thermo.utils.rst` - utility/base-class API references.
3. `docs/thermo.interface.rst` - surface tension APIs.
4. `docs/thermo.functional_groups.rst` - RDKit-backed feature detection.
5. `docs/thermo.eos_alpha_functions.rst` - vectorized alpha-function routines.

## Validation docs/tests to pair with startup
- `tests/test_t_dependent_property.py`
- `tests/test_tp_dependent_property.py`
- `tests/test_interface.py`
- `tests/test_utils.py`
- `tests/test_functional_groups.py`

## File inventory
- `docs/property_objects.rst` | title: Introduction to Property Objects
- `docs/thermo.utils.rst` | title: Utilities and Base Classes (thermo.utils)
- `docs/thermo.interface.rst` | title: Interfacial/Surface Tension (thermo.interface)
- `docs/thermo.functional_groups.rst` | title: Functional Group Identification (thermo.functional_groups)
- `docs/thermo.eos_alpha_functions.rst` | title: Cubic Equation of State Alpha Functions (thermo.eos_alpha_functions)
