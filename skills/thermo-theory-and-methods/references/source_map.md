# thermo source map: Theory and Methods

Use this map after checking `references/doc_map.md`.

## Environment bootstrap (run once from repo root)
```bash
python -m pip install -e .
python -m pip install -e .[test]
```

## Fast source navigation
- `rg -n "class (PR|SRK|PRMIX|SRKMIX)|eos_lnphi|eos_G_dep" thermo/eos.py thermo/eos_mix.py`
- `rg -n "volume_solutions_|PR_lnphis|SRK_lnphis" thermo/eos_volume.py thermo/eos_mix_methods.py`

## Function-level source entry points
- `thermo/eos.py` | symbols: `GCEOS`, `PR`, `SRK`, `eos_lnphi`, `eos_G_dep` | behavior: pure-component cubic EOS and departures.
- `thermo/eos_mix.py` | symbols: `GCEOSMIX`, `PRMIX`, `SRKMIX`, `one_minus_kijs` | behavior: mixture EOS state and mixing rules.
- `thermo/eos_volume.py` | symbols: `volume_solutions_NR`, `volume_solutions_fast`, `volume_solutions_Cardano`, `volume_solution_polish` | behavior: cubic root solvers and polishing.
- `thermo/eos_mix_methods.py` | symbols: `a_alpha_and_derivatives`, `PR_lnphis`, `SRK_lnphis`, `eos_mix_lnphis_general` | behavior: low-level lnphi/derivative helpers.
- `thermo/eos_alpha_functions.py` | symbols: `PR_a_alphas_vectorized`, `SRK_a_alphas_vectorized`, `Twu91_alpha_pure` | behavior: alpha-function calculations used by EOS classes.
- `thermo/phases/ceos.py` | symbols: `CEOSPhase`, `CEOSGas`, `CEOSLiquid` | behavior: phase wrappers over EOS objects.

## Simulation-ready checks
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
pytest -q tests/test_eos.py tests/test_eos_mix.py tests/test_eos_volume.py tests/test_eos_mix_methods.py
```

## Validation checkpoints
- Root volumes and fugacities are finite and smooth under small state perturbations.
- EOS and volume-solver tests pass before changing numerical methods.
