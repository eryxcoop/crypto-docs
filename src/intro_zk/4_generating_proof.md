
# Generating the proof
After arithmetization, both the prover and verifier have a system of polynomial constraints $R(\vec{x})$ that has the following property:

$$
R(\vec{x}) = 0 \iff \vec{x}\text{ is the execution of the agreed program}
$$

A good prover knows the correct witness $\vec{w}$, because it has executed the program. Suppose we encode $\vec{w}$ as a polynomial $w(x)$ such that $w(\omega^i)=\vec{w}_i$ via interpolation. We call this polynomial $w(x)$ the witness polynomial.  For the zero knowledge property to hold, the prover does not want to reveal the witness to the verifier. The witness could have secret values. The goal of $P$ is to convince $V$ that he knows $w(x)$ such that $R(w(\omega^i))=0$ for all $i$, without revealing $w(x)$. Let's see the whole protocol and then understand how it works.

## Protocol
### Commitment phase
1. $P$ computes $Z(x)=\prod_i(x-\omega^i)$
2. $P$ computes $H(x)$ such that $R(w(x))=H(x)Z(x)$.
3. Commit witness: $P$ sends $[W]=\text{commit}(w)$ to $V$.
4. Commit quotient: $P$ sends $[H]=\text{commit}(H)$ to $V$.
### Challenge and open phase
5. $V$ sends a challenge $z$ to $P$.
6. $P$ opens $H(z), w(z)$ and sends the proofs and results to $V$.

### Verify phase
7. $V$ verifies $H(z)$ and $w(z)$ using the proof following the PCS.
8. $V$ computes $R(w(z))$ and $Z(z)$.
9. $V$ checks that $R(w(z))=H(z)Z(z)$.

Note: The name of the phases are not common to ZK literature, they where chosen in this text for educational purposes. $H(x)$ is sometimes called the **quotient polynomial**.

## Why it works
### Commitmment phase
In the commitment phase the prover computes a polynomial $Z(x)$ called the **zerofier**. $Z(x)$ has all the roots $\omega, \omega^2, \dots$ we want our polynomial $R(w(x))$ to have. Note that $R(w(\omega^i))=0$ for all $i$ if and only if $\vec{w}$ is a valid witness for the program logic encoded in $R$. If $P$ is a good prover with a valid witness, then $H(x)$ such that $R(w(x))=H(x)Z(x)$ can be computed, because $Z(x)$ divides exactly $R(w(x))$. After that, the prover commits $w(x)$ and $H(x)$ and cannot change them anymore. Note that $[W], [H]$ do not reveal any information about the polynomials. These are only commitments.

### Challenge phase and open
In the challenge phase, $V$ sends a challenge $z$. A **challenge** it's just a random field element, that $P$ cannot know in advance. Note that $P$ has committed to $w(x)$ and $H(x)$ before knowing $z$. $P$ cannot have used $z$ to craft malicious polynomials during the commitment phase. Now, using the PCS, $P$ can send the values $w(z), H(z)$ and the proof, without revealing any additional information of the polynomial. Special care must be taken, because $V$ could choose $z$ in order to reveal secret values of $w(x)$. For this purpose, $z$ is normally chosen out of the domain $\omega^i$.

### Verify phase
Note that the verifier could ask for $w(\omega^i)$ for all $i$ using the polynomial commitment scheme. Then, $V$ could check that $R(w(\omega^i))=0$, and infer that $\vec{w}$ is a valid witness, meaning that $P$ knows the correct execution of the program! But this has two problems. First, it would not be *zero-knowledge*, because $P$ would reveal the witness to $V$. Second, it would not be succint, because the proof size would be proportional to the witness, which is large. To account for this the **Schwartz-Zippel** lemma is used. This lemma implies that if you compare two polynomials $p, q$ at a random value $\beta$ and it happens that $p(\beta)=q(\beta)$, then $p$ and $q$ are the same polynomial with overwhelming probability. This is one of the reasons why we expressed the logic of our computation as a system of polynomial constraints. We want to use this lemma.

First of all, $V$ uses the PCS to verify the points $H(z)$ and $w(z)$ come from the committed polynomials in the first phase, and they have not been changed by a malicious $P$ after sending the challenge $z$. Then $V$ computes $R(w(z))$. At this moment, $V$ is making sure that the logic of the agreed program $R$ is used. $V$ also computes the zerofier at $z$, $Z(z)$. Next, the verifier checks that $R(w(z))=H(z)Z(z)$. If the prover $P$ was good, then this equation must hold. Remember that the constraint system $R(x)$ works by discriminating if $w(\omega^i)$ is a valid witness or garbage. So if $Z(x)=\prod_i(x-\omega^i)$ divides exactly $R(w(x))$, then $w(x)$ must correspond to a valid witness. The trick for succintness is that we can check that $R(w(\omega^i))=0$ for all $i$, by just checking one equality at $z$ holds, by using polynomial division and the **Schwartz-Zippel** lemma. 

## Non-interactiveness
The protocol we showed is interactive, in the sense that $P$ and $V$ talk to each other. We can make this protocol non-interactive if we can get $P$ to output the complete proof $\pi$ without asking $V$ for a challenge $z$. The challenge step is sometimes replaced with a trick called the **Fiat-Shamir** heuristic. This is just a way for $P$ to sample randomness without asking $V$ for it. For this to work, $P$ uses the unpredictable output of a hash function over things $P$ can't control: the public input, the result of the commitments, etc... The structure used in the algorithms to deliver this randomness is sometimes called the **transcript**.



