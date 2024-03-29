# Polynomial commitment scheme
Polynomial commitment schemes are protocols between the prover and the verifier. In this protocol, $P$ commits to a polynomial $p$ without revealing any information about $p$ to $V$. We say a **PCS** is **binding** because the prover can't change the polynomial $p$ once he has commited to it (otherwise $V$ would notice). A commitment is also **hiding** because it doesn't reveal unintended information about $p$ to $V$.

A normal exchange between $P$ and $V$ would include three operations called $\text{commit}$, $\text{open}$ and $\text{verify}$. Suppose $P$ and $V$ want to have an interaction over some polynomial $t(x)$:
- `[T] := commit(t)`: the prover computes $[T]$, the commitment of $t(x)$,  and sends it to $V$. From now on, $P$ can't change $t$ or lie about values $t(x)$ without $V$ noticing. We'll see why this is useful later. At the moment, you can see $[T]$ as a big number with special properties.
- `proof, t_z := open(t, z)`: the verifier still doesn't know anything about $t$, but he wants to know $t(z)$ for a particular $z$. So the prover computes $t(z)$ and a `proof` to convince $V$.
- `verify(proof, t_z, [T])`: using the commitment $[T]$ and the proof, $V$ verifies that $t_z = t(z)$. The verifier can keep asking the prover to "reveal" the value of $t$ at many points, and $P$ can't lie about them, $V$ can always verify that they come from the same polynomial $t(x)$.

## Batch commitments
Usually we want to commit several polynomials, and open them up all at the same point. Most polynomial commitment schemes can be adapted to batch commit and batch open the polynomials, taking advantage of this fact to increase performance.

Example:
- `[T] := batch_commit(t1, t2, ..., tn)`}
- `proof, t1_z, t2_z, ..., tn_z := batch_open(t1, t2, ..., tn, z)`
- `batch_verify(proof, t1_z, t2_z, ..., tn_z)`

## Trusted setup
Some polynomial commitment schemes require a **trusted setup**. This is a ceremony that generate keys $V_k, P_k$ for the prover and the verifier. These keys are needed for the PCS to work. Generally, a **trusted** third party creates some secret $s$ and generates $V_k, P_k$ from $s$. Then $s$ **MUST** be thrown away, $s$ is called the **toxic waste**. Having access to the toxic waste would make the protocol insecure.

We have only seen PCS at an abstract level. That's all we need to understand how ZK proofs work. You can later on read about specific schemes suchs as KZG or FRI. Next we'll see how arithmetization and PCSs are used together to generate a ZK proof.
