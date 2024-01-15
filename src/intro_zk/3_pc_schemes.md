# Polynomial commitment scheme
In this protocol, the prover commits to a polynomial $p$ without revealing any information about $p$ to $V$. We say a PCS is **binding** because the prover can't change the polynomial $p$ once he has commited to it (otherwise $V$ would notice). A commitment is also **hiding** because it doesn't reveal unintended information about $p$ to $V$.

A normal exchange between $P$ and $V$ would include three operations called $\text{commit}$, $\text{open}$ and $\text{verify}$. Suppose $P$ and $V$ want to have an interaction over some polynomial $t(x)$:
- `[T] := commit(t)`: the prover computes $[T]$, the commitment of $t(x)$,  and sends it to $V$. From now on, $P$ can't change $t(x)$ without $V$ noticing. (For now you can see $[T]$ as a big number with special properties)
- `proof, t_z := open(t, z)`: the verifier still does not know anything about $t(x)$, but he wants to know $t(z)$. So the prover computes $t(z)$ and a `proof` to convince $V$.
- `verify(proof, t_z, [T])`: $V$ verifies that $t_z = t(z)$. The verifier can keep asking the prover to "reveal" the value of $t(x)$ at several points, and $P$ can't lie about them, they will always come from the same polynomial $t(x)$.

## Batch commitments
Usually we want to commit several polynomials, and open them up all at the same point. Most polynomial commitment schemes can be adapted to batch commit and batch open the polynomials, taking advantage of this fact to increase performance.

Example:
- `[T] := commit(t1, t2, ..., tn)`}
- `proof, t1_z, t2_z, ..., tn_z := open(t1, t2, ..., tn, z)`
- `verify(proof, t1_z, t2_z, ..., tn_z)`

## Trusted setup
Some polynomial schemes require a **trusted setup**. This is a ceremony that generate keys $V_k, P_k$ for the prover and the verifier. These keys are needed for the PCS to work. Generally, a **trusted** third party creates some secret $s$ and generates $V_k, P_k$ from $s$. Then $s$ **MUST** be thrown away and is called the **toxic waste**. 

Next we'll how arithmetization and PCS are used together to generate a ZK proof.
