# thermo documentation map: Advanced and Specialized Topics

Use docs first, then move to `references/source_map.md` for module-level behavior checks.

## Start docs for advanced simulation work
1. `docs/index.rst` - install and package-level navigation.
2. `docs/examples.rst` - notebook/example entry points.
3. `docs/thermo.flash.rst` - flash APIs before deep internals.
4. `docs/thermo.stream.rst` - stream/equilibrium process-level APIs.
5. `docs/thermo.units.rst` - pint-wrapped execution expectations.

## Validation docs/tests to pair with startup
- `tests/test_stream.py`
- `tests/test_units.py`
- `tests/test_chemical.py`
- `tests/test_phase_identification.py`
- `tests/test_thermal_conductivity.py`
- `tests/test_viscosity.py`
- `tests/test_volume.py`

## File inventory
- `docs/index.rst` | title: Thermo: Thermodynamics and Phase Equilibrium component of Chemical Engineering Design Library (ChEDL)
- `docs/examples.rst` | title: Example uses of Thermo
- `docs/thermo.activity.rst` | title: Activity Coefficients (thermo.activity)
- `docs/thermo.bulk.rst` | title: Bulk Phases (thermo.bulk)
- `docs/thermo.chemical.rst` | title: Legacy Chemicals (thermo.chemical)
- `docs/thermo.chemical_package.rst` | title: Chemical Constants and Correlations (thermo.chemical_package)
- `docs/thermo.datasheet.rst` | title: Creating Property Datasheets (thermo.datasheet)
- `docs/thermo.electrochem.rst` | title: Electrochemistry (thermo.electrochem)
- `docs/thermo.equilibrium.rst` | title: Equilibrium State (thermo.equilibrium)
- `docs/thermo.flash.rst` | title: Flash Calculations (thermo.flash)
- `docs/thermo.heat_capacity.rst` | title: Heat Capacity (thermo.heat_capacity)
- `docs/thermo.interaction_parameters.rst` | title: Interaction Parameters (thermo.interaction_parameters)
- `docs/thermo.law.rst` | title: Legal and Economic Chemical Data (thermo.law)
- `docs/thermo.mixture.rst` | title: Legacy Mixtures (thermo.mixture)
- `docs/thermo.permittivity.rst` | title: Permittivity/Dielectric Constant (thermo.permittivity)
- `docs/thermo.phase_change.rst` | title: Phase Change Properties (thermo.phase_change)
- `docs/thermo.phase_identification.rst` | title: Phase Identification (thermo.phase_identification)
- `docs/thermo.property_package.rst` | title: Legacy Property Packages (thermo.property_package)
- `docs/thermo.stream.rst` | title: Streams (thermo.stream)
- `docs/thermo.thermal_conductivity.rst` | title: Thermal Conductivity (thermo.thermal_conductivity)
- `docs/thermo.units.rst` | title: Support for pint Quantities (thermo.units)
- `docs/thermo.vapor_pressure.rst` | title: Vapor Pressure and Sublimation Pressure (thermo.vapor_pressure)
- `docs/thermo.viscosity.rst` | title: Viscosity (thermo.viscosity)
- `docs/thermo.volume.rst` | title: Density/Volume (thermo.volume)
