# Other arguments
There are some common zk-proofs/arguments that are helpful when designing proving systems. You could use these as auxiliary proofs to help your proving system craft the "main proof". Let's see an example.

## Permutation argument
The permutation argument is a zk proof on itself. The goal of this argument is to convince $V$ that two sets $S=\{s_1, \dots, s_n\}$, $T=\{t_1, \dots, t_n\}$ contain the same elements, without revealing their content. This could be helpful in the context of a bigger proof where you are already using $S$ and $T$ for some stuff, and you now want to prove the equality of the two sets. As before, the permutation argument will consist of a constraint system.

Out constraint system is inspired in the following fact:

$$S=T \iff \prod_{i=1}^n (x-s_i)=\prod_{i=1}^n (x-t_i)$$

The equation on the right contains polynomials with roots on $s_i$ and $t_i$ respectively. If the sets are equal, then the polynomials are equal, and the reverse is also true. Note that other simpler strategies don't work. For example if we just checked $\prod_{i=1}^n s_i = \prod_{i=1}^n t_i = l$ there would be no guarantees about $S=T$. In this case, there are different ways to obtain the same result $l=8$: $S=\{2, 4\}, T=\{1, 8\}$. But polynomials do work, and that is good because as we've already learned, we want to create a system of polynomial constraints in order to make a zk-proof.

### Protocol
$P$ and $V$ agree to work over a finite field $\mathbb{F}$ and a domain $\Omega=\{\omega^0, \omega^1, \dots, \omega^n\}$. $P$ has polynomials $s(x), t(x)$ such that $s(\omega^i)=s_i$ and $t(\omega^i)=t_i$. These polynomials encode $S$ and $T$. $P$ will prove $V$ that $S=T$, without revealing the contents of the two sets.

#### Commitment phase
1. $P$ computes $[S]=\text{commit}(s(x))$.
2. $P$ computes $[T]=\text{commit}(t(x))$.
3. $P$ sends $[S], [T]$ to $V$.

#### Commitment phase 2
4. $V$ sends a challenge $\beta$ to $P$.
5. $P$ interpolates $z(x)$ such that $$z(\omega^i)=\prod_{j=0}^i \frac{(\beta - s(\omega^j))}{(\beta - t(\omega^j))}$$
6. $P$ computes $[Z]=\text{commit}(z(x))$.
7. $P$ sends $[Z]$ to $V$.

#### Challenge and open phase
8. $V$ sends a challenge $\gamma$ to $P$.
9. $P$ opens $s(x), t(x), z(x)$ at $\gamma$.

#### Verify phase
10. $V$ verifies $s(\gamma), t(\gamma), z(\gamma)$.
11. $V$ verifies that the constraint system holds:

$$z(\omega \gamma) = z(\gamma) * \frac{(\beta - s(\gamma))}{(\beta - t(\gamma))}$$

The polynomial constraint system for this particular zk-proof is the following:

> We'll interpolate over $\Omega$ the polynomial $z(x)$:
>  
> This polynomial is the cumulative product of the polynomials we've seen in the last section, until the $i$-th element. 
>
> #### Boundary constraint:
>  $$z(\omega^n) = 1 $$
> We want the sets to be equal. Once the cumulative product in $z(x)$ has reached the end, all the elements are the same
> and $\prod_{i=1}^n (x-s_i)=\prod_{i=1}^n (x-t_i)$ holds. In particular, evaluating that expression in $\beta$ also holds.
>
> #### Transition constraint:
>  $$z(\omega x) = z(x) * \frac{(\beta - s(x))}{(\beta - t(x))}$$
>
> This constraint makes sure that $z(x)$ actually is the cumulative product.

1. $P$ computes $z(x)$ such that 
2. $P$ commits the polynomial $[Z]=\text{commit}(z(x))$ and sends it to $V$.
3. $V$ sends a challenge $\beta$ to $P$.
4. $P$ opens the polynomial $z_{\beta}, \pi = \text{open}(z(x), \beta)$ and sends it to $V$.
5. $V$ verifies $z(x)=z_{\beta}$ using $\text{verify}([Z], \pi, z_{\beta})$ 
6. $V$ verifies 

