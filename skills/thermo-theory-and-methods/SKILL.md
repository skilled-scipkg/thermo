---
name: thermo-theory-and-methods
description: This skill should be used when users ask about theory and methods in thermo; it prioritizes documentation references and then source inspection only for unresolved details.
---

# thermo: Theory and Methods

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## High-Signal Playbook
### Route conditions
- Route to `thermo-getting-started` for end-to-end setup/flash recipes and first runnable workflows (`docs/tutorial_phases_and_flash.rst`).
- Route to `thermo-inputs-and-modeling` for choosing Gibbs-excess/activity models or phase model composition (`docs/activity_coefficients.rst`, `docs/thermo.phases.rst`).
- Route to `thermo-api-and-scripting` when users ask about property object method dispatch/extrapolation APIs rather than EOS internals (`docs/property_objects.rst`).

### Triage questions
- Pure-component EOS or mixture EOS?
- Which EOS family/variant (PR, SRK, translated, PRSV, etc.)?
- Is the user diagnosing root/volume solver behavior or only using high-level outputs?
- Are they asking about stability criteria (`G_dep`, phase selection) or fugacity derivatives?
- Do they need high precision/robustness over speed?
- Are units expected in molar SI or via wrappers?

### Canonical workflow
1. Choose `thermo.eos` for pure systems or `thermo.eos_mix` for mixtures (`docs/cubic_equations_of_state.rst`, `docs/thermo.eos.rst`, `docs/thermo.eos_mix.rst`).
2. Initialize EOS objects with critical properties and a full state spec (`T/P`, `T/V`, or `P/V`) (`docs/cubic_equations_of_state.rst`).
3. Evaluate primary thermodynamic outputs (roots, fugacities, departures, derivatives) from object properties (`docs/cubic_equations_of_state.rst`).
4. Use `to(...)`/`subset(...)` to explore neighboring states or reduced compositions (`docs/cubic_equations_of_state.rst`).
5. For pure systems with multiple roots, compare stability via Gibbs departure (`docs/cubic_equations_of_state.rst`).
6. If cubic root quality is questionable, inspect/choose numerical or high-precision solvers in `thermo.eos_volume` (`docs/thermo.eos_volume.rst`).
7. For performance-sensitive loops, inspect `thermo.eos_mix_methods` only after correctness is established (`docs/thermo.eos_mix_methods.rst`).

### Quick simulation start
```bash
python - <<'PY'
from thermo import PR, PRMIX

eos = PR(Tc=507.6, Pc=3025000.0, omega=0.2975, T=400.0, P=1e6)
print(round(eos.V_l, 9), round(eos.V_g, 9), eos.more_stable_phase)

mix = PRMIX(
    T=115.0, P=1e6,
    Tcs=[126.1, 190.6], Pcs=[33.94e5, 46.04e5],
    omegas=[0.04, 0.011], zs=[0.5, 0.5],
    kijs=[[0.0, 0.0289], [0.0289, 0.0]],
)
print([round(v, 3) for v in mix.fugacities_l])
PY
pytest -q tests/test_eos.py tests/test_eos_mix.py tests/test_eos_volume.py
```

Validation checkpoints:
- Pure EOS returns finite liquid/gas roots and a stable phase.
- Mixture fugacities are finite and EOS test suite subset passes.

### Minimal working example
```python
from thermo import PR, PRMIX

eos = PR(Tc=507.6, Pc=3025000.0, omega=0.2975, T=400.0, P=1e6)
print(eos.V_l, eos.V_g, eos.more_stable_phase)
print(eos.Psat(400.0), eos.Psat(400.0, polish=True))

mix = PRMIX(
    T=115.0, P=1e6,
    Tcs=[126.1, 190.6], Pcs=[33.94e5, 46.04e5],
    omegas=[0.04, 0.011], zs=[0.5, 0.5],
    kijs=[[0.0, 0.0289], [0.0289, 0.0]],
)
print(mix.fugacities_l)
```
Source: `docs/cubic_equations_of_state.rst`.

### Pitfalls/fixes
- EOS APIs are molar SI; if mass-based quantities are expected, move to `Phase` or units wrappers (`docs/cubic_equations_of_state.rst`).
- Floating-point equality checks on EOS identities will show tiny residuals; use tolerances (`docs/cubic_equations_of_state.rst`).
- Mixture equilibrium is not globally trivial; results depend on algorithm/initialization choices (`docs/cubic_equations_of_state.rst`).
- Analytical cubic solvers can lose robustness in extreme regimes; use numerical/high-precision fallbacks in `thermo.eos_volume` (`docs/thermo.eos_volume.rst`).
- High-performance helper functions in `thermo.eos_mix_methods` are overflow/internal style APIs; avoid using them as first interface (`docs/thermo.eos_mix_methods.rst`).

### Convergence/validation checks
- Recompute pressure from solved volumes and verify residual is near zero (`docs/cubic_equations_of_state.rst`).
- For pure systems with two roots, ensure chosen phase matches lower Gibbs departure (`docs/cubic_equations_of_state.rst`).
- Compare `Psat(..., polish=False)` vs `Psat(..., polish=True)` to detect solver sensitivity (`docs/cubic_equations_of_state.rst`).
- For mixtures, verify fugacity/`lnphi` trends stay smooth under small `T/P/z` perturbations.

## Scope
- Handle questions about theoretical background and algorithmic methods.
- Keep responses focused on model assumptions, solver behavior, and defensible validation checks.

## Primary documentation references
- `docs/thermo.eos_volume.rst`
- `docs/thermo.eos_mix_methods.rst`
- `docs/thermo.eos_mix.rst`
- `docs/thermo.eos.rst`
- `docs/cubic_equations_of_state.rst`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use tutorials/examples as executable usage patterns when available.
- Use tests as behavior or regression references when available.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the ranked source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `docs/Examples`
- `docs/plots`

## Test references
- `tests`

## Optional deeper inspection
- `thermo`

## Source entry points for unresolved issues
- `thermo/eos.py`
- `thermo/eos_mix.py`
- `thermo/eos_volume.py`
- `thermo/eos_mix_methods.py`
- `thermo/eos_alpha_functions.py`
- `thermo/phases/helmholtz_eos.py`
- `thermo/phases/gibbs_eos.py`
- `thermo/phases/ceos.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" thermo`).
