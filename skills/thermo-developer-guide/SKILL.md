---
name: thermo-developer-guide
description: This skill should be used when users ask about developer guide in thermo; it prioritizes documentation references and then source inspection only for unresolved details.
---

# thermo: Developer Guide

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```
RDKit is also required for group-contribution and functional-group workflows.

## High-Signal Playbook
### Route conditions
- Route to `thermo-api-and-scripting` for consumer API usage that does not involve extending internals (`docs/thermo.utils.rst`, `docs/property_objects.rst`).
- Route to `thermo-inputs-and-modeling` when the request is model selection/calibration rather than implementing new contribution methods (`docs/activity_coefficients.rst`).
- Route to `thermo-advanced-topics` for module-level legacy APIs not tied to group-contribution internals.

### Triage questions
- Are you using an existing group-contribution estimator or extending one?
- Is RDKit available in the runtime/test environment?
- Is the input representation a SMILES string or an RDKit molecule object?
- Do you need Tc/Pc estimates (`Wilson_Jasperson`), Vc (`Fedors`), broad property estimates (`Joback`), or vdW R/Q (`Bondi`)?
- Are uncertainty/status flags being checked and propagated?
- Which tests will validate the change (`tests/test_joback.py`, `tests/test_wilson_jasperson.py`, `tests/test_fedors.py`)?

### Canonical workflow
1. Confirm RDKit path and molecule parsing path (SMILES vs rdkitmol) before method logic (`thermo/group_contribution/joback.py`, `thermo/group_contribution/wilson_jasperson.py`, `thermo/group_contribution/fedors.py`).
2. Choose estimator based on required output set (`docs/thermo.group_contribution.wilson_jasperson.rst`, `docs/thermo.group_contribution.joback.rst`, `docs/thermo.group_contribution.fedors.rst`, `docs/thermo.group_contribution.bondi.rst`).
3. Run estimator and capture all status outputs/flags, not only scalar estimates (`thermo/group_contribution/fedors.py`, `thermo/group_contribution/wilson_jasperson.py`).
4. If fragmentation or increment coverage fails, inspect functional-group matches and fallback behavior (`thermo/group_contribution/joback.py`, `thermo/functional_groups.py`).
5. Validate against existing tests and documented examples before integrating changes.
6. Only then update docs/maps and extend coverage for new groups/rules.

### Quick simulation start
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

Validation checkpoints:
- `Wilson_Jasperson`/`Fedors` return finite estimates plus diagnostic flags.
- Group-contribution test subset passes before extending catalogs.

### Minimal working example
```python
from thermo.group_contribution import Wilson_Jasperson
from thermo.group_contribution.fedors import Fedors

Tc, Pc, miss_Tc, miss_Pc = Wilson_Jasperson('CCC1=CC=CC=C1O', Tb=477.67)
print(Tc, Pc, miss_Tc, miss_Pc)

Vc, status, unknown_atoms, unknown_bonds, unknown_rings = Fedors('CCC(C)O')
print(Vc, status, unknown_atoms, unknown_bonds, unknown_rings)
```
Sources: `thermo/group_contribution/wilson_jasperson.py`, `thermo/group_contribution/fedors.py`, `tests/test_wilson_jasperson.py`, `tests/test_fedors.py`.

### Pitfalls/fixes
- RDKit is mandatory for these paths; handle missing dependency explicitly (`thermo/group_contribution/joback.py`, `thermo/group_contribution/wilson_jasperson.py`, `thermo/group_contribution/fedors.py`).
- Joback fragmentation is incomplete by design; missing groups/failed splits are expected for some molecules (`thermo/group_contribution/joback.py`).
- `Fedors` returns diagnostic flags (`status`, unknown atoms/bonds/rings); do not ignore them (`thermo/group_contribution/fedors.py`).
- `Wilson_Jasperson` returns missing-increment booleans; treat those as low-confidence estimates (`thermo/group_contribution/wilson_jasperson.py`).
- Group-contribution extensions should keep tests deterministic and input-representation stable.

### Convergence/validation checks
- Ensure `status == 'OK'` for `Fedors` and flags are false on trusted molecules (`tests/test_fedors.py`).
- Ensure `missing_Tc_increments`/`missing_Pc_increments` are false on benchmark molecules (`tests/test_wilson_jasperson.py`).
- Compare new estimates against at least one existing fixture or documented example before merging.
- Run targeted tests for touched estimators and related functional-group matchers.

## Scope
- Handle questions about developer architecture, extension points, and contribution workflow.
- Keep responses extension-safe and test-oriented.

## Primary documentation references
- `docs/thermo.group_contribution.wilson_jasperson.rst`
- `docs/thermo.group_contribution.joback.rst`
- `docs/thermo.group_contribution.fedors.rst`
- `docs/thermo.group_contribution.bondi.rst`

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
- `tests/test_joback.py`
- `tests/test_wilson_jasperson.py`
- `tests/test_fedors.py`

## Optional deeper inspection
- `thermo`

## Source entry points for unresolved issues
- `thermo/group_contribution/joback.py`
- `thermo/group_contribution/wilson_jasperson.py`
- `thermo/group_contribution/fedors.py`
- `thermo/group_contribution/bondi.py`
- `thermo/group_contribution/group_contribution_base.py`
- `thermo/group_contribution/__init__.py`
- `thermo/functional_groups.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" thermo/group_contribution thermo/functional_groups.py`).
