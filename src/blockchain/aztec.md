# Aztec

Different blockchains provide different functionality. Bitcoin offered decentralized transactions and a currency. Ethereum added turing complete smart contracts, basically a decentralized VM. Aztec aims to build a layer of privacy on top of Ethereum. Other projects such as Z-cash have done this before, but the goal of Aztec is to make private smart contracts, and not just money exchange like Z-cash.

|Technology|Programmable|Private|
|----------|------------|-------|
|Bitcoin   | No         | No    |
|Ethereum  | Yes        | No    |
|Zcash     | No         | Yes   |
|Aztec     | Yes        | Yes   |

Similarly to Starknet, Aztec is an L2 layer developed on top of Ethereum. This means its architecture is composed of two parts:

- **On chain contracts**: smart contracts deployed to Ethereum that are in charge of guaranteeing the Aztec system is changing from state to state according to a published protocol. This contracts can be programmed in languages like **Solidity**, that are compatible with the Ethereum instruction set.
- **Off chain contracts**: smart contracts running outside Ethereum, inside the Aztec ecosystem. This contracts can be programmed using Noir, a language that compiles to zk circuits, where you can use private input.

The contracts in the L1 and L2 interact with each other to provide a number of benefits. The two main benefits are: privacy and reduced cost*. To achieve privacy, the smart contracts living in the L2, written in **Noir**, can handle private inputs. A prover runs locally on the machine of each user, and generates a proof that he did not mess up with the protocol. We'll see later how you can do this. To achieve reduced costs, a strategy similar to that of Starknet is used. Basically, a sequencer batches transactions and a single validify proof is crafted, to show to a verifier in the L1 that all the transactions in the batch where correctly reflected in the state of the Aztec system. The cost of running the verifier is splitted between all the users that made a transaction, reducing the transaction fees.

The prover in Aztec uses a protocol called **Plonk**, a protocol that the company has developed. Its arithmetization is through **circuits** and uses **KZG** as a polynomial commitment scheme. The first known open-source implementation due to Aztec is **Barretenberg**, which is written in C++. There are plans to build a prover in Rust (**Barrustenberg**), and also to change the protocol a little bit (**Honk** prover).

## Further reading
- [UTXOs and Nullifiers](https://medium.com/aztec-protocol/fully-confidential-ethereum-transactions-aztec-networks-privacy-architecture-274f968b13d4)
- [Aztec docs](https://docs.aztec.network/concepts/foundation/main)
