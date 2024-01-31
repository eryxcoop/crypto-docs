# MSM

## Pippenger
This is the basic algorithm to optimize MSM. It doesn't leverage curve structure or anything.

- Explained here: [Pippenger Algorithm Explained](https://blog.lambdaclass.com/eyes-on-the-prize/)
- Explained also in section 4 [here](https://eprint.iacr.org/2022/1400.pdf).

## GLV Decomposition
- Original paper: [Faster Point Multiplication on Elliptic Curves with Efficient Endomorphisms](https://www.iacr.org/archive/crypto2001/21390189.pdf)
- To read about endomorphisms: "Guide to Elliptic Curve Cryptography" Darrel Hankerson, Alfred Menezes, Scott Vanstone
- [Link](https://github.com/mratsim/constantine/blob/master/constantine/math/elliptic/ec_multi_scalar_mul_parallel.nim#L582) it seems that GLV stops being useful after 2^12 points.

## Gnark ZPrize optimizations
The original paper is ["EdMSM: Multi-Scalar-Multiplication for SNARKs and Faster Montgomery multiplication"](https://eprint.iacr.org/2022/1400.pdf). This paper explains Pippenger at section 4.

### Curve forms and coordinate system
- Mixed addition is adding a point in affine to a point in other coordinate system.
- In a short weierstrass curve, you have different types of coordinate system: affine, projective, jacobian and extended jacobian.
- Uses jacobian extended coordinates. These are the fastest when a curve is in short weierstrass form.
- Uses twisted edwards for mixed addition over BLS* curves.
- Twisted edwards for mixed addition is even better than extended jacobian in short weierstrass. It also uses an extended coordinate system over twisted edwards.

### Signed digit decomposition
- When splitting each coefficient $a_i$ of $b$ bits into chunks $a_{ij}$ of $c$ bits, you end up with $2^c$ possibilities for each coefficient. You can reduce this to $2^{c-1}$ using "signed digit decomposition". When a chunk $a_ij$ is lower than $2^{c-1}$ it stays the same. When a chunk $a_ij$ is greater than $2^{c-1}$, then you can substract $2^c-1$ from it an add $1$ to the next chunk $a_{i,j+1}$. 

### Parallelization
- Parallelization for pippenger in the "submsms", the first step.


## Resources
- [Constantine](https://github.com/mratsim/constantine/blob/master/constantine/math/elliptic/ec_multi_scalar_mul_parallel.nim#L582): Nim repo 
- [Trello card](https://trello.com/c/m4KuyPIt/36-investigar-sobre-optimizaciones-de-msm)
