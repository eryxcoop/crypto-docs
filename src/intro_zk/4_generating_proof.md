
# Generating the proof
After arithmetization, both the prover and verifier have a system of polynomial constraints $R(x)$ that has the following property:

$$
R(\vec{w}) = 0 \iff \vec{w}\text{ is the execution of the agreed program}
$$

A good prover knows the correct $\vec{w}$, because it has executed the program. Suppose we encode $\vec{w}$ as a polynomial $w(x)$ such that $w(\omega^i)=\vec{w}_i$ via interpolation. We call this polynomial $w(x)$ the **witness** polynomial.  The prover does not want to reveal the witness to the verifier (it could have secret values). So $P$ must convince $V$ that he knows $w(x)$ such that $R(w(\omega^i))=0$ without revealing $w(x)$. Let's see the whole protocol and then understand how it works.

## Generic protocol
1. $P$ sends $[W]=\text{commit}(w)$ to $V$.
2. $P$ computes $H(x)$ such that $R(w(x))=H(x)Z(x)$ where $Z(x)=\prod_i(x-\omega^i)$.
3. $P$ sends $[H]=\text{commit}(H)$ to $V$.
4. $V$ sends a challenge $z$ to $V$.
5. $P$ opens $H(z), w(z)$ and sends the proofs and results to $V$.
6. $V$ verifies $H(z)$ and $w(z)$ using the PCS.
7. $V$ computes $R(w(z))$ and $Z(z)$.
8. $V$ checks that $R(w(z))=H(z)Z(z)$.

- Step 1: The prover commits to $w$, from now on it is fixed and $P$ cannot lie about the values $w(x)$ he sends to the prover.
- Step 2: Compute $H(x)$. the only reason $H(x)$ exists is because $Z(x)$ divides exactly $R(x)$. All the roots in $Z(x)$ are in $R(x)$, so $R(w(\omega^i))=0$ for all $i$.
- Step 3: $P$ commits to $H(x)$. It cannot change it anymore.
- Step 4: $V$ sends a challenge $z$. A **challenge** it's just a random field element, that $P$ cannot know in advance. This step is sometimes replaced by using the **Fiat-Shamir** heuristic to sample randomness. This makes the protocol non-interactive by letting $P$ sample randomness from the hash of the public input, commitments and interactions. The structure in charge of delivering this randomness is sometimes called the **transcript**. 
- Step 5: the prover computes $H(z), w(z)$ and sends them to the verifier. $P$ cannot lie about these values.
- Step 6: This step makes sure that $H(z)$ and $w(z)$ correspond to the commited $[H]$ and $[W]$. Note that this commitments were sent before knowing $z$.
- Step 7: Computes $R(x)$ at $w(z)$ and $Z(z)$. Here is where $V$ makes sure the proof is actually about the logic $R(x)$ of the agreed program.
- Step 8: Using the **Schwartz-Zippel** lemma $V$ convinces himself with overwhelming probability, that $R(w(x))=H(x)Z(x)$ (seen as polynomials) by just checking the equality at one random point $z$. This implies that $R(w(x))$ is zero over the domain $\omega^i$, that is, $w(x)$ must correspond to an execution of the program.

## Auxiliary arguments
There are some common "auxiliary" proofs that are helpful when designing proving systems.

### Permutation argument
The permutation argument is a zk proof on itself. The goal of this argument is to convince $V$ that two sets $S_1, S_2$ contain the same elements, without revealing its contents.
