# Hyperplonk
It reduces plonks proof generation time for two reasons:
- Eliminates the need to compute FFTs.
- Better handling of custom gates.

To achieve this, the strategy is to use a *ZeroCheck* IOP instead of a polynomial quotient. Let's see the different approaches:
- **Plonk**: uses circuit arithmetization, then interpolates the witness ($a, b, c$) and selectors (Q_l, Q_r, Q_m, Q_o, Q_c) in univariate polynomials over a domain $\omega^i$ in $\mathbb{F}_p$. 
- **Hyperplonk**: uses circuit arithmetization, it expresses the witness ($M$) and selectors ($S_1, S_2, S_3$) in multilinear polynomials (with multiple variables of at most degree 1) over a domain ${0, 1}^\mu$, called the Hypercube.

![image](src/images/hyperplonk.png)

The ZeroCheck requires the prover to do $s+d\mu$ group exponentiations, which is smaller than $ds$ in Plonk.
# Links
- [Hyperplonk paper](https://eprint.iacr.org/2022/1355.pdf)
- [Multilinear commitment scheme](https://eprint.iacr.org/2022/1010.pdf) check out protocol 2
- [Plonk paper](https://eprint.iacr.org/2019/953.pdf)