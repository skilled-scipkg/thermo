# thermo documentation map: Theory and Methods

Use docs first, then move to `references/source_map.md` for solver/function internals.

## Start docs for EOS method work
1. `docs/cubic_equations_of_state.rst` - practical EOS identities, phase selection, stability checks.
2. `docs/thermo.eos.rst` - pure-component EOS class APIs.
3. `docs/thermo.eos_mix.rst` - mixture EOS class APIs.
4. `docs/thermo.eos_volume.rst` - cubic volume solver implementations.
5. `docs/thermo.eos_mix_methods.rst` - low-level mixture derivative helpers.

## Validation docs/tests to pair with startup
- `tests/test_eos.py`
- `tests/test_eos_mix.py`
- `tests/test_eos_volume.py`
- `tests/test_eos_mix_methods.py`

## File inventory
- `docs/cubic_equations_of_state.rst` | title: Introduction to Cubic Equations of State
- `docs/thermo.eos.rst` | title: Cubic Equations of State (thermo.eos)
- `docs/thermo.eos_mix.rst` | title: Cubic Equations of State for Mixtures (thermo.eos_mix)
- `docs/thermo.eos_volume.rst` | title: Cubic Equations of State Volume Solvers (thermo.eos_volume)
- `docs/thermo.eos_mix_methods.rst` | title: Cubic Equations of State Utilities (thermo.eos_mix_methods)
