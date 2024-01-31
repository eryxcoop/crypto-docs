# Bitcoin's lack of anonymity
Bitcoin was not designed to allow anonymous transactions. What Bitcoin has is pseudonymity in the form of addresses. Users of Bitcoin found clever ways to obfuscate the history of transactions to make it hard for anyone to trace funds. One strategy used by Bitcoin clients is to generate new addresses instead of reusing always the same one. Another strategy is the use of *laundries*. These are entities that for a fee would mix different users' funds to hinder traceability. This is practiced despite the clear risk of not getting the funds back. All this motivated the research to come up with a privacy layer over Bitcoin. The first such solution is Zcash.


# Zcash
Zcash is a layer 1 blockchain running a fork of Bitcoin. It supports transactions, like in Bitcoin, but where the sender, the receiver, and the amount are encrypted before being broadcasted. It uses ZK proofs to ensure consistency of the entire ledger.

## Zerocoin
The development of Zcash has its origins in 2013 in the [paper](https://zerocoin.org/media/pdf/ZerocoinOakland.pdf) "Zerocoin: Anonymous Distributed E-Cash from Bitcoin". In that paper, the authors proposed the first scheme providing user anonymity. There were previous works in that direction, however, Zerocoin was the first one to achieve anonymity without relying on trusted parties. This was previous to all the modern proving systems for arbitrary computations. Therefore, Zerocoin achieves anonymity through the use of zero-knowledge proofs of knowledge designed for particular problems similar to Schnorr's protocol.

The idea behind Zerocoin was the following. Every user can execute a transaction that would put some bitcoin into a pool of anonymous coins, called *zerocoins*. Later on, the user can spend from this pool without revealing their identity, or the exact anonymous coin it is using. The magic of zero-knowledge proofs and the clever protocol handles the consistency of the system.

![image](../images/zcash.png)

## Zerocash
Zerocoin had several problems. The main one was proof sizes. These were in the order of 45kb, making anonymous transactions too large with slow verification times (450 ms). Another problem is that Zerocoin only supports zerocoins of a fixed amount and does not provide means to divide them. 

So, in 2014 the [paper](https://eprint.iacr.org/2014/349.pdf) "Zerocash: Decentralized Anonymous Payments from Bitcoin" was published. It introduces the second generation of these ideas. This time, the proposed protocol achieves privacy and not only anonymity. This means it hides account balances, transaction amounts, and user identities from public view. While doing so, it also improved on every other aspect of Zerocoin: proof sizes were reduced to 1kb, verification times of around 6 ms, anonymous transactions allowed variable amounts, and direct anonymous payments to a user's address.
Zerocash leverages one of the first zk-SNARKS for arbitrary programs called *Pinocchio*.

## Zcash
Zcash is the blockchain that implements the Zerocash [protocol](https://zips.z.cash/protocol/protocol.pdf). Throughout the subsequent years, Zcash evolved following the advances in Zero Knowledge protocols. Different versions of Zcash receive different codenames: Sapling, Sprout, and Orchard. The proving systems used were: BCTV14 in Sapling, Groth16 in Sprout, and Halo2 in Orchard. 


# References
- [Zerocoin](https://zerocoin.org/media/pdf/ZerocoinOakland.pdf).
- [Zerocash](https://eprint.iacr.org/2014/349.pdf).
- [Pinocchio](https://eprint.iacr.org/2013/279.pdf).
- [Zcash 2023 protocol](https://zips.z.cash/protocol/protocol.pdf).
- [BCTV14](https://eprint.iacr.org/2013/879.pdf).
- [Groth16](https://eprint.iacr.org/2016/260.pdf).
- [Halo](https://eprint.iacr.org/2019/1021.pdf) and [Halo2](https://zcash.github.io/halo2).
- [CVE-2019-716](https://nvd.nist.gov/vuln/detail/CVE-2019-7167).
