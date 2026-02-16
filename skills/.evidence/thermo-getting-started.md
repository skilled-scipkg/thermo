# Evidence: thermo-getting-started

## Primary docs
- `docs/tutorial_phases_and_flash.rst`
- `docs/cubic_equations_of_state.rst`
- `docs/chemical_package_tutorial.rst`
- `docs/property_objects.rst`
- `docs/activity_coefficients.rst`

## Primary source entry points
- `skills/thermo-getting-started/references/doc_map.md`
- `thermo/property_package.py`
- `thermo/chemical_package.py`
- `thermo/chemical_utils.py`
- `thermo/chemical.py`
- `thermo/activity.py`
- `thermo/phases/__init__.py`
- `thermo/flash/__init__.py`
- `thermo/utils/tp_dependent_property.py`
- `thermo/utils/t_dependent_property.py`
- `thermo/utils/mixture_property.py`
- `thermo/phases/virial_phase.py`
- `thermo/phases/phase_utils.py`
- `thermo/phases/phase.py`
- `thermo/phases/petroleum.py`
- `thermo/phases/ideal_gas.py`
- `thermo/phases/iapws_phase.py`
- `thermo/phases/helmholtz_eos.py`
- `thermo/phases/gibbs_excess.py`
- `thermo/phases/gibbs_eos.py`

## Extracted headings
- (none extracted)

## Executable command hints
- (none extracted)

## Warnings and pitfalls
- * Allow options to restart a flash from a nearby previously calculated result, with options to skip checking the result for stability
- Note that as floating points are not perfectly precise, some small error may be shown but great care has been taken to minimize this.
- .. warning::
- which has a typographical error please report it to the
- Where speed has been important so far, these integrals have been implemented analytically in a property object's :obj:`calculate_integral <thermo.utils.TDependentProperty.calculate_integral>`  and :obj:`calculate_integral_over_T <thermo.utils.TDependentProperty.calculate_integral_over_T>` method; otherwise the integration is performed numerically.
- The fitting function returns the regressed coefficients, and optionally some statistics. The mean absolute relative error or "MAE" is often a good parameter for determining the goodness of fit; Antoine yielded an error of about 1%.
- Fitting coefficients is a complicated numerical problem. MINPACK's lmfit implements Levenberg-Marquardt with a number of tricks, and is used through SciPy in the fitting by default. Other minimization algorithms are supported, but generally don't do nearly as well. All minimization algorithms can only converge to a minima near points that they evaluate, and the choice of initial guesses is quite important. For many methods, there are several hardcoded guesses. By default, each of those guesses are evaluated and the minimization is initialized with the best guess. However, for maximum accuracy, `multiple_tries` should be set to True, and `all` initial guesses are converged, and the best fit is returned.
- It can be quite important to use the :obj:`to_T_xs <thermo.unifac.UNIFAC.to_T_xs>` method re-use parts of the calculation; for UNIFAC, several terms depends only on temperature. If the 200 component calculation is repeated with those already calculated, the timings are 3.26 ms in CPython, 127 µs with numba, and 125 µs with PyPy.
