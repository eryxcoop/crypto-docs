# Math intro
## Groups
A **group** is a fancy mathematical name for a set of elements that are related to each other. These elements are related through an operation called the **group operation**. This operation must be **associative** and have a **neutral element**. The number of elements in the set is called the **order** of the group.

> **Example**: the integers $\mathbb{Z}$ together with the addition form a group because addition is associative
> $$(a + b) + c = a + (b + c)$$
> and also they have a neutral element represented by $0$:
> $$a + 0 = a$$

## Finite groups
A **finite group** is a group where the set of elements is finite. An example of a finite group is the integers modulo $n$ and the operation addition. In modular arithmetic two elements $a$ and $b$ are equivalent if they have the same remainder modulo $n$. This is noted by $a \equiv b \mod n$.

> **Example**: 4 and 12 are equivalent modulo 8, because they have the same remainder when dividing by 8.
> $$4 \equiv 12 \mod 8$$

> **Example**: the integers modulo 8, noted by $\mathbb{Z}_8$, together with the modular addition form a group:
> $$(a + b) + c \equiv a + (b + c) \mod 8$$
> and also they have a neutral element represented by $0$:
> $$a + 0 = a \mod 8$$
> The sets of elements in the group is $\{0, 1, 2, 3, 4, 5, 6, 7\}$.

## Multiplicative groups
When choosing $p$ as a **prime** number $\mathbb{Z}_p$ has an additional property. We define the multiplication as $ab \equiv c \mod p$. Where $ab = pk + c$ for max $k$*. That is, $c$ is the result of multiplying $a$ and $b$ and taking the remainder of dividing by $p$. If $p$ is prime, we also know that for all $a \in \mathbb{Z}_p$, there exist an element $a^{-1}$ such that $a a^{-1}=1 \mod p$. That is, every element has a multiplicative inverse and we can take $1$ as the neutral element. This is due to **Fermats little theorem**. The only detail is we have to remove the $0$, because it doesn't have a multiplicative inverse. We denote this set without the zero $\mathbb{Z}_p^{\times}$

> **Example**: the integers modulo 7 without the zero, noted by $\mathbb{Z}_7^{\times}$, together with the modular multiplication form a multiplicative group:
> $$(a * b) * c \equiv a * (b * c) \mod 7$$
> and also they have a neutral element represented by $1$:
> $$a * 1 = a \mod 7$$

Note that addition and multiplication are associative and commutative. In this case, the groups are called **commutative or abelian groups**. The only thing that changes between a multiplicative group and additive group is its notation (e.g.: using $ag$ vs $g^a$ or using $1$/$0$ for the neutral element). Mathematically they both have the same properties.

## Generators
Let $\mathbb{G}$ be a group. The **span** of an element $g \in \mathbb{G}$ is the set you get by repeatedly applying the group operation over that element. Using multiplicative notation, the span of $g$ is ${g^0, g^1, \dots}$. This set is not infinite. Note that a finite group has finite elements, so every element $a \in \mathbb{G}$ will eventually cycle and $a^i=a$ for some $i$. If the span of $g$ generates all the elements in $\mathbb{G}$, we say is a **generator** of the group. This will happen if the size of the span of $g$ is the same as the order of the group. Some elements $a \in \mathbb{G}$ will generate a subset of all the elements. In this case we will say $a$ is a generator of that subgroup.

> **Example**: the following table shows the structure of the multiplicative subgroup $\mathbb{Z}_7^{\times}$.
>
> | generator | span               |
> |-----------|--------------------|
> |  1        | {1}                |
> |  2        | {1, 2, 4}          |
> |  3        | {1, 2, 3, 4, 5, 6} |
> |  4        | {1, 2, 4}          |
> |  5        | {1, 2, 3, 4, 5, 6} |
> |  6        | {1, 6}             |
>
> All the elements in the group generate something. In this case, the spans of 3 and 5 each generate the whole group. The elements 2 and 4 belong
> to a subgroup where all the properties of a group also hold.

The **lagrange theorem** states that the order of any subgroup in $\mathbb{G}$ must divide the order of the whole group.

> **Example**: The multiplicative subgroup associated with $\mathbb{Z}_7^{\times}$ has order 6. It's subroups have order 1 (trivial subgroup), 2 and 3. They all divide 6.

## Finite field
A **finite field** is similar to a **group** but instead of only one operation it defines: addition, substraction, multiplication and division. As natural, substraction is the inverse of addition and the same applies for multiplication and division. As before, the number of elements in the set is called the **order** of the field. Note that $\mathbb{Z_p}$ with $p$ prime, if we consider all the operations we've seen, can be viewed as a finite field noted as $\mathbb{F}_p$.

> **Note**: $\mathbb{F}_p$ has order $p$, and also has a multiplicative group associated with it of order $p-1$, when considering all non-zero elements. It will be common to refer to this as the group associated with $\mathbb{F}_p$.

## Further reading
- [An introduction to mathematical cryptography](https://link.springer.com/book/10.1007/978-0-387-77993-5)
- [Efficient Software-Implementation of Finite Fields with Applications to Cryptography](https://link.springer.com/article/10.1007/s10440-006-9046-1)
- To compute the multiplicative inverse efficiently, look into the [extended euclidean algorithm](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm#:~:text=The%20extended%20Euclidean%20algorithm%20is%20the%20essential%20tool%20for%20computing,fields%20of%20non%2Dprime%20order).
- To compute the exponentiation efficiently, look at [Exponentiation by Squaring](https://en.wikipedia.org/wiki/Exponentiation_by_squaring).
