# McCall Job Search Model 1 — Practice Notebook (README)

## What this notebook does
This notebook implements **McCall Job Search Model 1** with **discrete wage offers**. Each period, an unemployed worker receives a wage offer \(w\) drawn from a known distribution \(q(w)\). The worker either **accepts** (and then earns that wage forever) or **rejects** (earns unemployment benefit \(c\) and draws again next period). Future payoffs are discounted by \(\beta\).

## Key parameters
- `w` : wage grid (e.g., 10 to 60)
- `q` : probability mass function over the wage grid (must sum to 1)
- `c` : unemployment benefit (paid when rejecting)
- `beta` : discount factor (patience)

## Core outputs (what you should look for)
- **Value function** \(v^*(w)\): computed by **value iteration**
- **Reservation wage** `w_bar` (=\(\bar w\)): the cutoff wage
  - Policy rule: **accept if** \(w \ge \bar w\), **reject otherwise**
- **Accept probability (weighted)** `accept_prob`:
  \[
  p = \sum_i q_i \mathbf{1}\{w_i \ge \bar w\}
  \]
- **Grid fraction accepted (unweighted)** `accept.mean()`:
  - This is just the fraction of grid points above \(\bar w\) (not the true probability).

## What you implemented (by sections)
1. **Value Iteration Solver**
   - Iterates the Bellman update until convergence.
   - Tracks convergence using the sup-norm error and iteration count.
2. **Reservation Wage Computation**
   - After convergence, computes `w_bar` and forms the threshold policy.
3. **Offer Distributions**
   - Built and compared multiple wage-offer distributions:
     - Uniform
     - Discretized lognormal
     - Two-normal mixture
     - Beta-binomial (QuantEcon-style example)
4. **Comparative Statics**
   - Varies `c` (keeping `q` and `beta` fixed) and plots:
     - `w_bar` vs `c`
     - accept probability vs `c`
   - Varies `beta` (keeping `q` and `c` fixed) and plots:
     - `w_bar` vs `beta`
     - accept probability vs `beta`
   - Note: plots can look **step-like** because wages are on a discrete grid, so the accept set changes only when \(\bar w\) crosses a grid point.
5. **Simulation + Theory Check**
   - Simulates many job search spells using the computed policy.
   - Compares simulation averages to geometric-theory predictions using `p`:
     - \( \mathbb{E}[\text{periods until accept}] \approx 1/p \)
     - \( \mathbb{E}[\text{rejections before accept}] \approx (1-p)/p \)

## How to run
1. Run the notebook cells from top to bottom.
2. In the “Baseline run” cell, choose one distribution by setting `q = ...`.
3. Re-run:
   - the baseline solve cell,
   - the plots,
   - and the simulation cell to see how results change.

## Experiments to try
- Increase `c` and observe:
  - `w_bar` increases
  - accept probability decreases
  - average waiting time increases
- Increase `beta` and observe:
  - `w_bar` increases
  - accept probability decreases
  - average waiting time increases
- Switch distributions (`q_uniform`, `q_lognormal_discretized`, `q_mixture_normals`, `q_beta_binomial`) and compare how the shape of `q(w)` affects `w_bar` and waiting times.

## Dependencies
- `numpy`
- `matplotlib`
- `mpmath` (used for Beta-binomial PMF)

## Notes / common issues
- If `converged` is `False`, try:
  - increasing `max_iter`,
  - loosening `tol` slightly,
  - or checking that `q` sums to 1 and matches the length of `w`.
- Make sure the Beta-binomial parameter `n` matches your wage grid length (`n+1`).

---
**Glossary**
- `w_bar` / \(\bar w\): **reservation wage** (accept cutoff)
- `accept_prob` / \(p\): probability an offer is acceptable under \(q(w)\)
- “Model 1”: permanent job once accepted, no separations, offers i.i.d.
