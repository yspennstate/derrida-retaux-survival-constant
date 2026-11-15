# Critical Derrida–Retaux recursions with nonuniversal survival probabilities

Paper, programs and data for

> Y. Shmalo, *Critical Derrida–Retaux recursions with nonuniversal survival probabilities*, November 2026.

For an integer m ≥ 2 the Derrida–Retaux recursion is X_{n+1} = (X_n^{(1)} + … + X_n^{(m)} − 1)^+, and the initial law is critical when (m−1) E[X_0 m^{X_0}] = E[m^{X_0}] < ∞. The conjectured asymptotic is n² P(X_n > 0) → 4/(m−1)². The paper shows:

- **Theorem 1.1.** For every m ≥ 2 there is a nonconstant critical law with lim sup n² P(X_n > 0) = ∞, with a finite tilted moment E[X_0^r m^{X_0}] of any prescribed order 0 < r < 2.
- **Theorem 1.2.** If G_n(m) → 1 and log Π_n / log n → β ∈ (0, ∞), then lim inf n² q_n ≤ β(β+2)/(2(m−1)²) ≤ lim sup n² q_n.
- **Theorem 1.3.** For the critical laws with P(X_0 = k) proportional to m^{−k} k^{−b}, 2 < b < 4, the bracket is b(b−2)/(2(m−1)²), which is below 4/(m−1)².
- **Corollary 1.4.** Under a finite third tilted moment the only possible limit is 4/(m−1)². Whether the limit exists in that case is open.

No proof depends on computation. Section 5 of the paper reports n² q_n for n ≤ 10,000.

## Layout

```
paper/main.tex, paper/main.pdf    the paper
code/                             programs (Python 3.13)
data/                             outputs used in Section 5
```

## Reproducing Section 5

All commands run from the repository root. The programs use the POSIX modules `os.nice` and `resource`, so they run on macOS or Linux.

```sh
python3 -m pip install numpy scipy mpmath matplotlib
python3 -m pip install --target code/vendor gmpy2==2.3.2 python-flint==0.9.0
python3 code/run_suite.py         # binary64 FFT runs, n <= 10,000, K = 10,128 -> data/m{2,3}_*.csv
python3 code/certified.py --m 2 --law two2 --N 10000 --K 10128 --out data/cert_m2_two2.csv
python3 code/run_cert_suite.py    # certified runs: m=2 b=3, m=3 two-point k=2, m=3 b=3
python3 code/run_cert_geom.py     # certified runs, geometric-type law
python3 code/run_sparse.py        # sparse law, FFT and certified
python3 code/check_float.py       # FFT with K = 20,128, for the cutoff comparison
python3 code/validate.py          # exact checks on small cases -> data/validation*.{csv,json}
python3 code/analyze.py           # tables -> data/numerical_results.md, fits -> data/fits.csv, figures
```

`code/run_long.py` runs the binary64 recursion to n = 100,000 (`data/long_m2_*.csv`). These runs are diagnostics: the two-point run drifts upward from rounding, and the paper does not use them.

The certified programs store each mass as an integer multiple of 1/(S m^k) with S = 2^112, raise the generating polynomial to the m-th power exactly (FLINT), round down, and propagate a transport-distance error bound with upward rounding. Each `cert_*.csv` row gives n, a midpoint value of q_n and n² q_n, the lower and upper endpoints of q_n as exact fractions, the rounding error bound and a bound on the tilted mass; each `cert_*.json` records the parameters and the final enclosure. The lower endpoint is justified by Lemma 5.2 of the paper (the computed law is stochastically dominated by the law of X_n), the upper endpoint by Proposition 5.1 and the transport bound in Section 5.1.

Binary64 values (`data/m*_*.csv` without the `cert_` prefix) carry the support bound of Proposition 5.1 but no rounding certificate. At n = 10,000 they differ from certified values by at most about 1.1 × 10⁻⁵ in n² q_n, which is why the paper prints them to four decimals.

`data/environment.json` records the platform of the original runs (macOS 15.5, arm64, Python 3.13.2).
