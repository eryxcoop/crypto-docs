# KZG
This is a polynomial commitment scheme due to [Kate, Zaverucha and Goldberg](https://www.iacr.org/archive/asiacrypt2010/6477178/6477178.pdf). It leverages the properties of elliptic curves such as **BLS12381**. They have two interesting properties. First, the discrete log problem is hard. Second they have a bilinear operation called pairing, that allows for homeomorphic encryption up to one multiplication.

## Setup
For KZG to work, an additional step is required at the beginning. This step is called the setup. First, everyone agrees on a group $\mathbb{G}$ and a generator $G$. Normally $\mathbb{G}$ is chosen as a high-order prime subgroup of a secure elliptic curve. Then, a trusted third-party samples a random $s$ and computes the following:

$$S=(G, sG, s^2G, \dots, s^nG)$$

Then, $s$ **must** be thrown away. Sometimes $s$ is called the toxic waste. If someone knew $s$ then the PCS would be insecure.

## Commit
In the commitment phase $P$ wants to commit to a polynomial $t=t_0+t_1x+t_2 x^2 +t_n x^n$. To do this, it computes:

$$[T] = t_0 G + t_1 s G + t_2 s^2 G + \dots + t_n s^n G=t(s)G$$

Using the homeomorphic properties of the elliptic curve, we can evaluate the encryption of any polynomial at $s$, by just knowing the encryptions of $s^i$.

## Open
The verifier asks for $t(z)$. The prover wants to send $t(z)$, without revealing additional information about $t$ (hiding). Also, he wants to convince $V$ that he's still using the same polynomial $t(x)$ (binding). To do this, $P$ computes an auxiliary polynomial $t'(x)=\frac{t(x)-t(z)}{x-z}$. Then, just like in the commitment step it computes:

$$\pi = t'_0 G + t'_1 s G + t'_2 s^2 G + \dots + t'_n s^n G=t'(s)G$$

Then $P$ outputs $(\pi, t_z)$ where $t_z=t(z)$.

## Verify
The verifier accepts if the following is true:

$$e([T], G)=e(\pi, (s - z) G) e(G, G)^t_z$$

This expression is equivalent to:

$$e(t(s)G, G)=e((\frac{t(s)-t(z)}{s-z})G, (s - z) G) e(G, G)^{t_z}$$

Because of the bilinearity, the pairings can be transformed to multiplications in the "plaintext" domain. Also, the multiplications in $\mathbb{F}_p$ can be transformed to additions. Then we have that the latter equation holds if and only if the following holds:

$$t(s)=\frac{t(s)-t(z)}{s-z} (s - z) + {t_z}$$

This will hold if and only if $t_z=t(z)$ so the verifier is convinced.
