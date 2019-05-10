---
description: >-
  Archetype is a smart contract development solution dedicated to contract
  safety and consistency.
---

# What is Archetype?

The archetype project is developed by [edukera](https://edukera.com), and funded by [Tezos](https://tezos.com/).

## Introduction

A smart contract is a program which is executed on the blockchain. They were introduced in 2015 by [Ethereum](https://www.ethereum.org/) and this really unleashed the full potential of the blockchain technology because smart contracts allow to read and write virtually any data on the blockchain.

A smart contract is similar to a stored procedure on a public distributed database. As such, they must ensure the logical consistency and integrity of the data.

So what’s the problem with smart contracts?

In a nutshell, they break one of the key principles of the blockchain, which is the _trust-less principle_.

What is meant by _trust-less_ is that, with a blockchain, there is no need for a trusted third party to be confident in the fact the blockchain is doing what it is intended to, which is to manage transactions. Indeed blockchains are secure by design:  they are decentralised and immutable; plus every change in the system is validated by a consensus algorithm.

A smart contract is a _standard_ program, and, as such, it does not come with any guarantee that it will function correctly. We all have in mind the DOA incident: a bug in the smart contract made it possible to withdraw several times the money you would put in the contract. As a result, more than 100 millions dollars were lost.

It’s a highly non trivial question to decide whether a program is correct or not, even for the programmer. Before deploying a smart contract, current development best practices include a phase of testing. However, testing is limited by the capacity to imagine relevant tests and, even when a critical property is identified, testing is limited by the capacity to cover all possible situations of execution. 

## Services

### Verification

### Simulation

### Documentation

### Execution

## A single language

## Integration in IDE

## A library of contract archetypes  

Archetype comes with several dozen of examples.

{% page-ref page="contracts-examples/escrow.md" %}

{% page-ref page="contracts-examples/auction.md" %}

{% page-ref page="contracts-examples/tokens/" %}

