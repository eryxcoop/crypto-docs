# Starknet
At this point you should understand that Ethereum is just a decentralized virtual machine, where anybody can pay money in exchange for running a bit of code. The code is called a smart contract, and when you run or deploy a contract, this is called a transaction. Ethereum has a protocol that guarantees the immutable integrity of all the previous states of this machine, and that guarantees that it evolved correctly.

The problem with ethereum is that transactions are expensive. Each transaction executes a **smart contract** and the fee you have to pay is proportional to the amount of computation your contract does. If you have a small program you may pay less **gas**, if you have a large program you pay more **gas** ([Costs reference](https://www.evm.codes/?fork=shanghai)). The cost of gas depends on the number of transactions that the system has to handle. Unfortunately, this means that when many transactions are in demand, the cost rises too much.

This is sometimes called the blockchain **trilemma**: you can't have decentralization, security and **scalability** (low costs) all at the same time. To solve this, L2 layers such as Starknet appeared.

Ethereum is very powerful because it can run turing complete smart contracts. This makes it possible for people to do stuff outside Ethereum, and generate cryptographic proofs that guarantee they sticked to a particular protocol. This cryptographic proofs can then be verified by a contract on Ethereum.

An L2 layer is a blockchain that runs in parallel to Ethereum. Transactions can be executed there instead of Ethereum. Somehow, this transactions are batched. A batch of T transactions in L2 is translated to just only one transaction in the L1, this means that the cost of one transaction can be splitted between T users, reducing the cost. The mechanism has to be good enough, to guarantee the integrity of the L2 blockchain through only one transaction in the L1.

A good strategy to do this is to use *zero knowledge*. L2 layers using zk to reduce transaction fees in L1, are sometimes called *zk rollups*.

## Starknet architecture
Let's see how starknet can reduce the transaction fees.

![image](../images/starknet_archi.png)

The strategy to do so is the following:
1. Alice wants to reduce the fees she's paying for ethereum transactions.
2. Alice freezes 100 eth in a smart contract living in the L1 belonging to Starknet.
3. Alice can make transactions with other people who have also frozen their eth in Starknet.
4. Starknet uses a server (off-chain) to batch this transactions and create a block $B$.
5. Starknet uses zero knowledge to prove that applying the transactions in $B$ moves the starknet state from $S_1$ to $S_2$. This is done also off chain.
6. A smart contract living in Ethereum, owned by starknet, verifies that the zk-proof is correct.

The verifier in step 6 is sometimes referred as **zl roll-up contract**.

The state of starknet is encoded in the root of a merkle tree. When transactions are applied, the merkle tree is modified and the new root is computed. The zk-program generates a proof that all this process was done correctly. This roots are called **state commitments** and are public parameters of the zk-program. This commitments are stored in the L1, guaranteeing the integrity of the L2. At step 6 we have cryptographic certainty that all the transactions where executed correctly and that starknet is not doing anything nasty. People can see the transactions that where executed in L2's in the L1. There's an unexpensive way to do this through **calldata**, that is, sending data as the input parameter of a dumb smart contract.

The contracts in Starknet are written in a language called **Cairo**. The specifications of this language have been translated to a system of polynomial constraints, also called AIR (Arithmetic intermediate representation). This constraints can be used to apply a polynomial commitment scheme to produce zk-proofs. The PCS used in starknet is called FRI, and the prover is called Stark prover, because it follows the Stark protocol. This protocol was inventend in part by Eli Ben Sasson, the co-founder and CEO of Starknet.

The transactions are executed twice*. The first time to check that it is a well-formed program, in the sense that it complies with the starknet protocol and it doesn't throw a runtime exception. After this first execution, an execution trace is generated, then a proof can be made out of this.

## Further reading
- [Ethereum ZK Roll ups](https://ethereum.org/en/developers/docs/scaling/zk-rollups)
