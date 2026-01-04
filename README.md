# Photoionization cross sections (H 1s and He 1s) — notebooks

This repository set contains two Jupyter/Colab notebooks implementing numerical calculations of photoionization cross sections using a partial-wave treatment of continuum states, and (for helium) a self-consistent field (SCF) effective potential.

## Contents

- **Notebook 1 — Hydrogen (1s) photoionization**
  - Solves the radial Schrödinger equation for the **continuum** partial wave with **\(\ell=1\)** in a Coulomb potential (\(Z=1\)).
  - Normalizes the continuum solution by fitting the **asymptotic Coulomb form**.
  - Computes the dipole radial matrix element and evaluates \(\sigma(E_\gamma)\) on a grid of photon energies.

- **Notebook 2 — Helium (1s) photoionization**
  - Computes the **He ground-state** radial orbital via an **SCF loop** (Hartree + local exchange / Hartree–Slater).
  - Uses the converged density \(\rho(r)\) to build an effective potential \(V_{\mathrm{eff}}(r)\).
  - For each continuum momentum \(k\), solves the radial equation for the **\(\ell=1\)** channel in \(V_{\mathrm{eff}}\), performs asymptotic normalization, and computes \(\sigma(E_\gamma)\).

> In the He notebook, the SCF bound-state energy is printed during iterations until convergence.

---

## Theory summary (what the notebooks compute)

Both notebooks use the **dipole approximation** with the angular selection rule \(\Delta \ell = \pm 1\). For ionization from an initial \(1s\) state (\(\ell_i=0\)), only the continuum \(\ell_f=1\) channel contributes.

### Radial equation (in atomic units)
For the reduced radial function \(u(r)=rR(r)\):
\[
u''(r)=2\big(V_{\mathrm{eff}}(r)-E\big)u(r)+\frac{\ell(\ell+1)}{r^2}u(r).
\]

### Dipole matrix element and cross section (used numerically)
A radial integrand of the form
\[
f(r)=R_{1s}(r)\,R_{k1}(r)\,r^3
\]
is integrated up to an automatically chosen cutoff \(r^\star\), yielding \(I(k)\).
The photoionization cross section is then computed (up to the constants used in each notebook) as:
\[
\sigma(k)\propto E_\gamma(k)\left(\frac{I(k)}{k}\right)^2,
\quad
E_\gamma(k)=\frac{k^2}{2}-E_{\mathrm{ion}}.
\]

---

## Requirements

Typical Python scientific stack:

- Python 3.x
- `numpy`
- `scipy`
- `matplotlib`

The He notebook additionally uses:
- `scipy.special.gamma` (complex gamma; used to estimate Coulomb phase)
- `scipy.optimize.curve_fit` and `scipy.optimize.brentq`
- `scipy.integrate.solve_ivp`

If you run on Google Colab, these dependencies are normally preinstalled.

---

## How to run

### In Google Colab
1. Upload the notebook(s) to Colab (or open from Drive).
2. Run cells from top to bottom.
3. For the He notebook comparison plot:
   - ensure the reference file exists at the expected path, e.g. `/content/he1s.txt`
   - ensure you execute the cell that **writes** the CSV before executing the cell that **reads** it

### Locally (Jupyter)
1. Create/activate an environment, install dependencies:
   ```bash
   pip install numpy scipy matplotlib
   ```
2. Launch Jupyter and run the notebooks in order.

---

## Outputs

- Produces a CSV file:
  - `outputs/sigma_vs_photon_energy.csv`
  - columns: `photon_energy_eV, sigma_Mbarn`
- Plots:
  - \(\sigma(E_\gamma)\) on a log scale
  - optional comparison scatter plot vs reference data (`he1s.txt` or `h1s.txt`)

> If you see `FileNotFoundError: outputs/sigma_vs_photon_energy.csv not found`, it usually means:
> - the “save CSV” cell was not executed in the current runtime, or
> - you restarted the runtime and lost generated files, or
> - your working directory differs from where you saved the CSV.
>
> In Colab, you can check with:
> ```python
> !ls -R
> ```

---

## Notes on numerical details / stability

- **Asymptotic normalization (continuum):**  
  The continuum reduced radial solution \(u(r)\) is fit at large \(r\) to a Coulomb-like asymptotic form to extract an amplitude \(A\). The notebook then normalizes as \(u \leftarrow u/A\).

- **Adaptive cutoff \(r^\star\):**  
  The dipole integrand is oscillatory and eventually negligible; the notebooks determine a cutoff where the integrand stays below a fraction of its maximum for a window of points.

---

## File layout assumptions

- The notebooks expects the reference dataset files:
  - `he1s.txt` and `h1s.txt` (two columns: photon energy [eV], cross section [Mbarn])
  - In Colab, the example code reads it from `/content/he1s.txt`

Adjust the path in the notebook if your reference file is elsewhere.

---

## Citation / attribution

If you use these notebooks in a report or publication, cite them and the reference dataset you compare against (if applicable). Add any relevant literature references (e.g., for Hartree–Slater exchange and Coulomb phase normalization).

---

## Contact

- Author: **Breno Martins de Oliveira**
- email: brenomartinso@usp.br or breno.martins05323@gmail.com
- Project context: Photoionization cross sections in multieletronic atoms (FAPESP)
