# Aztec

Different blockchains provide different functionality. Bitcoin offered decentralized transactions over a currency. Ethereum added turing complete smart contracts, basically a decentralized VM. Aztec aims to build a layer of privacy on top of Ethereum. Other projects such as Z-cash have done this, but the goal of Aztec is to make private smart contracts, and not just transactions.

|Technology|Programmable|Private|
|----------|------------|-------|
|Bitcoin   | No         | No    |
|Ethereum  | Yes        | No    |
|Aztec     | Yes        | Yes   |

Aztec is an L2 layer developed on top of Ethereum. This means its architecture is composed of two parts:

- **On chain contracts**: smart contracts deployed to ethereum that are in charge of guaranteeing the Aztec system is changing from state to state according to a protocol. This contracts can be programmed in languages like **Solidity**, that are compatible with the Ethereum instruction set.
- **Off chain contracts**: smart contracts running outside Ethereum, inside the Aztec ecosystem, in parallel. This contracts can be programmed using Noir, a language that compiles to zk circuits.

The contracts in the L1 and L2 interact with each other to provide a number of benefits. First, the smart contracts living in the L2, written in **Noir**, can handle private inputs. Also, the transactions can be pooled by a program called the sequencer into. A single zero knowledge proof can be generated for this whole batch. Finally, the smart contract living in the L1 can verify this single proof for the whole batch of transactions, guaranteeing the integrity of the Aztec chain.

## Further reading
- [UTXOs and Nullifiers](https://medium.com/aztec-protocol/fully-confidential-ethereum-transactions-aztec-networks-privacy-architecture-274f968b13d4)
- [Aztec docs](https://docs.aztec.network/concepts/foundation/main)
