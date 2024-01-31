
# Implementing DSA and ECDSA in Crystal


Github repository: [https://github.com/ajgara/crystal-primitives](https://github.com/ajgara/crystal-primitives)

## Introduction

Let's explore how we implemented the Digital Signature Algorithm (DSA) in Crystal. The code is available [here](https://github.com/ajgara/crystal-primitives) This includes the implementation of finite fields with their operations and the specific steps to sign a message using DSA.

[Crystal](https://crystal-lang.org/) is a high-level programming language. It is precompiled and has a garbage collector. Its speed is comparable to Rust and C.

[DSA](https://es.wikipedia.org/wiki/DSA), *Digital Signature Algorithm*, is an algorithm used for digitally signing a file and authenticating its owner. It is the algorithm that Bitcoin uses to allow wallet owners to authorize transactions.

[ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm), *Elliptic Curve Digital Signature Algorithm*, is an advanced digital signature algorithm that uses the principles of elliptic curve cryptography. It is known for its enhanced security and efficiency in comparison to traditional digital signature algorithms like DSA. ECDSA is widely used in various security protocols and systems, including SSL/TLS for secure web communications and in many cryptocurrencies. This algorithm allows for shorter key lengths while maintaining high security, making it a popular choice for modern cryptographic applications.


Crystal does not have developed libraries for cryptographic primitives, so this small work is a first approach in that direction.


## Finite Fields in Crystal
To start, we will tackle the implementation of finite fields in Crystal, including their basic operations. This lays the necessary foundation for the development of the DSA algorithm.

Implementing finite fields means creating a Finite Field Element object and implementing field operations on it.

The operations worth mentioning are:

#### Calculating the Inverse of an Element
This is done using the extended Euclidean algorithm. If $ F_p $ is a finite field of order $ p $, $ x \in F_p $, then, using the extended Euclidean algorithm, we can find $ s $ and $ t $ such that

$ sx + tp = \gcd(x, p) = 1 $
Since $ p \equiv 0 \mod p $, $ s $ is the inverse of $ x $ in $ F_p $.

#### Calculating the Square Root
The method for calculating the square root of an element in $ F_p $ depends on the prime $ p $. If $ p \equiv 3 \mod 4 $, the square root of an element $ x $ is $ x^{(p+1)/4} $. Otherwise, the [Tonelli-Shanks algorithm](https://en.wikipedia.org/wiki/Tonelli%E2%80%93Shanks_algorithm) must be used.

### Implementation

The first important decision to make is where to store the prime $ p $ that determines the order of the field. The approach suggested in the book "Programming Bitcoin" is to specify the prime every time you create a FiniteFieldElement. In [Sagemath](https://www.sagemath.org/), you first create the Field by assigning the prime and then use it to create your elements. Here, we opt for the simplest approach of all, which is to hardcode the prime $ p $ into the field. We will use $ p = 131071 $. It just so happens that $ p \equiv 3 \mod 4 $, so it is easy to calculate square roots in $ F_p $.

We use `BigInt` to store the class representatives of the field. We implement all operations modulo $ p $.


## Summary of the DSA Algorithm

We will provide a brief summary of the DSA algorithm, highlighting its key components and how they interact to provide secure digital signatures.

DSA is an algorithm for the owner of a private key to sign a message (actually the hash of a message) in such a way that another person can verify the signature using only the signer's public key.

There are some parameters that are public:

- The prime $ p $
- A prime $ q $ such that $ q \cdot z = p - 1 $ for some $ z $
- A randomly chosen value $ h $, usually $ h = 2 $
- $ g = h^z $

The signer has a private key $ x \in F_q $ whose public key is $ g^x \in F_p $
To sign the hash of a message $ m $, the signer:
1. Chooses randomly $ k \in F_q $ and calculates $ R = (g^k \mod p) \mod q $.
2. If $ R = 0 $, start again with another $ k $.
3. Calculate
$ s = \frac{m + xr}{k} \mod q $

4. The signature is the pair $ (r, s) $

The verifier receives the signature $ (r, s) $ and verifies it in the following way:
1. Calculates $ w = s^{-1} \mod q $.
2. Calculates $ u1 = mw \mod q $.
3. Calculates $ u2 = rw \mod q $.
4. Calculates $ v = (g^{u_1} y^{u_2} \mod p) \mod q $.
5. The signature is valid if $ v = R $.

I recommend doing the calculations on paper.

If someone wanted to forge the signature, that is, give a signature for the public key $ y $ without knowing $ x $, then they would need to find a value $ s $ such that $ w = s^{-1} $ is a solution of $ g^{wm} = g^k y^{-r} $, which is essentially solving the discrete logarithm.


## Challenges of Implementing DSA in Crystal

We implemented a `publicValues` class where we store the public parameters: $ p $, $ q $, $ z $, $ h $, $ g $.

We modeled the signer in a `Signer` class, which upon initialization generates a private key and derives the public key that can be requested via a method. It has a `sign` method that signs the hash of a message $ m $ and returns the signature $ (r, s) $.

We did not model the *Verifier*; instead, we implemented a function that takes the message and the signature and performs the calculations to verify it, returning True or False.

The main challenge is a consequence of the decisions we made regarding the prime $ p $ when implementing finite fields. We had *hardcoded* the prime as a variable of the class. However, when we want to implement DSA, we are going to make calculations modulo $ q $.
Attention to technical details is crucial. It's important to know when you're operating in $ F_p $, when you're in $ F_q $, and when you're in $ \mathbb{Z} $.
I chose to take a shortcut to get everything up and running more or less quickly. We work in $ F_p $ when we are in $ F_p $, then if I need to do the calculations modulo $ q $, I switch everything to `Int` and do the calculations modulo $ q $. It worked.


## Elliptic Curves

We modeled `EllipticCurvePoint`. Many of the decisions that need to be made when modeling the finite field reappear.
We *hardcoded* the curve parameters $ a $ and $ b $ in the point class. If later we want to generalize to any curve, we will see.

We can create a point on the curve from a pair $ (x, y) $ of `BigInt`, and the method to create the point verifies if the point is on the curve.

I also implemented a class method to create a curve point $ (x, y) $ from only an $ x $, to work more comfortably.

For the neutral element, we added an `is_infinity` attribute that can be `True` or `False` and a class method to create the infinity point.

We implemented equality, addition, and also scalar multiplication.

There is a detail about scalar multiplication. I was tempted to multiply by elements of the field but you can multiply by any integer.


## Challenges of Implementing ECDSA

As before, I implemented an `ECDSAPublicValues` class that holds the public values involved in the signature, which are the generator $ G $ of the subgroup of the curve, and its order $ q $. I also found it convenient to add the prime $ p $ of the finite field, understanding that it is repeated.
I implemented an `ECDSASigner` class for signing and a separate function for verification.
When creating a signer, I have to pass a random number generator `rng` that it uses to create a private key $ x $, and then a public key $ P := xG $.

To sign a message $ m $, I need its hash $ z \in F_q $. Then I randomly draw an element $ k $ from $ F_q $, obtain $ R := kG $, and keep $ r $, the first coordinate of $ R $, looking at it in $ F_q $.
I calculate $ s = \frac{z + rx}{k} $ in $ F_q $ and return the signature $ (r, s) $.

The verifier takes the signature and calculates $ u = z/s $ and $ v = r/s $. The signature is valid if the first coordinate of $ uG + vP $ is equal to $ r $. The difficulty I had was that everything has to be calculated in $ F_q $ and not in $ F_p $, since the periodicity of the subgroup generated by $ G $ and of the field $ F_p $ are different.


## Next Steps

There were many untidiness in the code. Many times I used integers modulo $ q $ instead of creating elements of a finite field $ F_q $. This could be quite tidied up. One issue is that the cryptographic protocols themselves do not clarify much where each element lives in each step. Sometimes they start in $ F_p $, then you think of it as an integer, then in $ F_q $. Sometimes they do not even clarify the existence of $ F_q $, only thinking of it as integers modulo $ q $.

This is something we could think about and ground.

There is also the option to continue with the book "Programming Bitcoin". And also refactor everything to be able to do calculations with more generic fields and curves.
