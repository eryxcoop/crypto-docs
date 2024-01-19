# Diffie Hellman
## Motivation
**Simmetric cryptography** algorithms are used to send secret messages between two parts $A$ and $B$. In the literature, $A$ and $B$ are usually called Alice and Bob. Alice and Bob first agree on a secret key $s$, an encryption function $E$ and a decryption function $D$. Then we have the following protocol:

1. Alice wants to send $m$ to Bob.
2. Alice encrypts $c=E(m, s)$.
3. Alice sends $c$ to Bob.
4. Bob recovers $m=D(c, s)$

A good encryption algorithm must hold the property that no one observing $c$ can obtain $m$, even knowing $D$ and $E$. That is, cryptographers assume everyone knows the algorithm (but not the secret $s$). This is known as the **Kerckhoffs principle**.

## Diffie Hellman
How can Alice and Bob agree on a secret $s$ securely if they live far away? Diffie-Hellman introduced a solution for this problem in a paper from 1976. Their protocol uses multiplicative groups to agree on a random number $s$ securely.

Setup: Alice and Bob agree on a finite group $\mathbb{G}$ and a generator $g$.
1. Alice samples a random $a$ and computes $A=g^a$.
2. Alice sends $A$ to Bob.
3. Bob samples a random $b$ and computes $B=g^b$.
4. Bob sends $B$ to Alice.
5. Alice computes $s=B^a$
6. Bob computes $s=A^b$

Note that $B^a=(g^b)^a=(g^a)^b=A^b=s$. So Alice and Bob reached to the same random number $s$.

The security of the algorithm depends on the chosen group $\mathbb{G}$. For some groups, computing $A=g^a$ is easy if you know $a$, but computing $a$ if you know $A$ and $g$ is hard. This is called the **Discrete Logarithm Problem** (the notation $a=\log_g(A)$ is not used, but you can see where the name comes from). This is why groups are useful in cryptography. You can use the fact that the discrete logarithm problem is hard, to "hide" stuff inside a group.

In step 1, Alice "hides" $a$ in the group by computing $g^a$. Then sending $A$ is safe if we assume the attackers cannot solve the discrete logarithm problem. Nowadays, is safe to assume this if we use $F_p$ for a really big $p$. But choosing a secure group $\mathbb{G}$ is a delicate decision. As hardware and algorithms get better, encryption algorithms have to keep up with it. Other groups such as those found on **elliptic curves** have become more popular for having a more difficult discrete logarithm problem.

The discrete logarithm problem is an example of a **one-way function**. That is, a function that is easy to compute but hard to invert.
