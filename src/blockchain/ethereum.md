# Ethereum

At its core, Ethereum can be thought of as a global, singleton machine that executes programs given by its users. Unlike traditional centralized systems, Ethereum operates on a blockchain.

## Blockchain
A blockchain is a particular type of Peer-to-Peer (P2P) network. These are networks like µTorrent, or the more early ones Ares and Napster. In those cases, the networks store files in a distributed manner without consistency or validity guarantees. In the same way that µTorrent stores files distributed in its nodes to share them, blockchains store another type of data that can be used with other means. 

Blockchains store an immutable, but extendable, list (or chain) of messages sent to them. The list gets extended in batches of new incoming messages. Each batch is called a "block" (hench a block-chain). Typically, these messages are called "transactions" because of the more currency-oriented use cases, but in general, a message is just a request sent by a user of the network indicating a desired action. Not every message is valid. Each blockchain has its protocol and accepts incoming messages that conform to certain rules and formats, and that must be consistent with all messages that have already been accepted in previous blocks. Only valid messages are stored in new blocks. Blockchains provide immutability guarantees of the blocks through mechanisms called consensus. Having such an immutable list that can be indefinitely extended with new incoming messages can be used to model different things.

Bitcoin is a blockchain. In this case, the transactions, or messages, that can be stored in the blockchain indicate the movement of funds between addresses of the chain's coin bitcoin. Valid transactions are the ones that request the movement of Bitcoin from accounts that have sufficient funds. That is, from an account for which there exist previous transactions already in the blockchain that moves to them the bitcoins they want to move.

## Ethereum
And so, Ethereum is another blockchain. It stores a chain of blocks that ends up modeling a global state machine. Each valid message in this blockchain is either a message that contains the bytecode of a program to store on the blocks, or a request to call a function of an already deployed program. Users and programs get addresses to identify them. And there's a third type of valid message: transferring ether, the Ethereum's coin, from one address to another. And yes, programs can hold ether. At deployment, programs have a hardcoded initial state defined by the developer.

In the same way that in Bitcon's chain, the list of transactions has all the information to recover the balance of every account, in Ethereum the list of blocks of messages is all one needs to recover the global state of the machine. This state, called The World State, is a table with columns "address, balance, program_state". The program state is the state of its variables. This column is empty for every account associated with a regular user that's not a program. The World State table is not stored in any block, but uniquely derived from the list of messages stored in the blockchain.

So in essence that is Ethereum. The consensus mechanism makes sure the blocks are immutable, and the state of the global machine can consistently evolve in time.

## Terminology
In the context of Ethereum and blockchain, programs like the ones in the previous section are called "smart contracts", and the messages sent to the blockchain are called "transactions". This is for historical reasons, but it's certainly misleading. That's why we avoided that terminology so far. Since it is so widely used in the field, we will switch to using it from now on.

## Token
So what is a Token anyway? A token is a smart contract that maintains a key-value table in one of its variables (therefore in its program state). This key value usually starts with one single entry like `{program-owner-address: 10000}`, denoting the number of "tokens" held by the owner. And has a `send` function taking a destination and a value `v`, and checks whether the sender of the message is in that table and has at least `d` tokens. Then it will subtract `d` from its balance and add `d` to the destination address.

