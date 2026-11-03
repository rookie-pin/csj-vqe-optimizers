# Classical optimizers in VQE for a lattice pionless EFT deuteron

Code and data for

P. Çifci and S. Akkoyun, *Convergence Behavior of Classical Optimizers in VQE for a Lattice Pionless EFT Deuteron Hamiltonian*, Cumhuriyet Science Journal (2026).

The Hamiltonian and its constants follow P. Çifci, S. Akkoyun and L. La Ronde, Eur. Phys. J. A 62, 199 (2026).

## Requirements

Python 3.10 and

```
pip install -r requirements.txt
```

The results in the paper were obtained with NumPy 2.2.1, SciPy 1.15.1, Qiskit 1.3.1 and Qiskit Nature 0.7.2.

## Running

```
Unzip csj_out_numpy/hist.zip so that the .npy files are in csj_out_numpy/hist/.
python deuteron_ed.py        # fits C to the deuteron binding energy
python csj_vqe.py            # all optimization runs and the tables
python csj_figs.py           # Figs. 2-4
```

`deuteron_ed.py` gives C = -0.344568 MeV, which `csj_vqe.py` uses as a fixed constant.

`csj_vqe.py --appendix` prints Appendix B from the saved runs without running anything. `--workers 4` runs in parallel from a terminal. Runs already in `runs.jsonl` are skipped, so an interrupted calculation can be continued. In Jupyter or Spyder the script only prints Appendix B unless `NOTEBOOK_ARGS` at the end of the file is set to `[]`.

All runs are deterministic for a given seed. The initial parameters come from `numpy.random.RandomState(seed)`, and the SPSA directions from `RandomState(seed + 10000)`.

## Output

Everything is written to `csj_out_numpy/`:

| File | Content |
|---|---|
| `runs.jsonl` | one record per run (680 runs), read by both scripts |
| `runs_flat.csv` | the same records as a table |
| `hist/` | energy of every evaluation, one `.npy` file per run |
| `hamiltonian_pauli.csv` | Pauli decomposition, Table A1 |
| `summary_stats.csv` | medians and quartiles, Table 2 |
| `appendix_b1.csv`, `appendix_b2.csv` | Table B1 |
| `environment.txt` | software versions and hardware |
| `figs/` | Figs. 2-4 as PNG and PDF |

Tables 3 and 4 are printed by `csj_vqe.py` and not saved to separate files.

Initialization ranges: `narrow` [0.1, 0.5], `near0` [-0.1, 0.1], `pm_pi` [-π, π], `zero_2pi` [0, 2π] rad. `protocol` is `free` for the deterministic optimizers and `fixed` for SPSA. `depth` is the number of ansatz layers.

Main columns of `runs_flat.csv` (energies in MeV):

- `nfev`: energy evaluations used, `evals_to_10keV`: evaluations until the energy first comes within 10 keV of E_ED
- `E`, `dE`: lowest energy of the run and its difference from E_ED = -2.224566 MeV
- `var_H`: Hamiltonian variance (MeV²)
- `Np`, `Nn`, `var_Np`, `var_Nn`: particle numbers of the final state and their variances
- `w_phys`: weight of the (1p,1n) sector
- `term`: `tolerance` (stopping criterion met) or `budget` (evaluation limit reached)

A run is counted as converged if `dE < 0.01` and `w_phys > 0.99`.

## License

MIT, see `LICENSE`.
