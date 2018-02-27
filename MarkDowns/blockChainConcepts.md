---
title: "BlockChainConcepts"
date: 2018-01-09T22:06:37-05:00
draft: true
---

Notes of basic block chain:



The "genesis" block:

- First block
- Can be configured (use to configuration if using private network).

A new block is created by miners.



A transaction is created by sending data with signature with private key.

- Private keys : used to sign transactions

A Public key is derived from the Private key and used to verify transactions

Ethereum Address is derived from the Public key.



The block chain is located on every computer, it is a massive distributed database.

You can access the block chain through clients (GoEthereum, Parity, Ethereum++, MataMask).

GoEthereum(geth) will download the whole block chain.



Smart contract is a piece of code running on the block chain

- it's a state machine
- Needs transactions to change state
  - change state through mining through transaction
- Can do logic operations



To deployment a smart contract:

1. Write a contract (code)
2. Compile to bytecode (Similar like Java compiled and run on JVM)
3. Send the compiled code to Network

**Once a smart contract is deployed, it is unable to change!**



To interact with the smart contract you write, you need to send the transaction.

Clients use **Application Binary Interface** to interact with the smart contact, basically it is a **Json File**, it contains all the information to interact with the smart contract.

---

The more complicated concepts of Block chain nodes:

An Ethereum block chain node implements the Ethereum Protocol, connects to other nodes, give the ability to access to the block chain and more.

Ethereumjs: simulation of the Ethereum protocol, and Unit-Testing smart contracts