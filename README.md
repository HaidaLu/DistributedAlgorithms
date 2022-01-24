

# DistributedAlgorithms

[TOC]



# Consensus

In the consensus problem, the processes propose values(Here we consider Single value consensus) and have to agree on one among these values.



Solving Consensus is key to solving:

1. Total order broadcast(aka Atomic broadcast)
2. Atomic commit(in database)
3. Terminating reliable broadcast: The process knows the broadcast has been terminated.
4. Dynamic group membership: The number and identity of the process in a group can be changed overtime and they have to agree who is in the group and who is not.
5. Stronger shared store models.



## Introduction

Alice: Where are we go today?

Bob: Let's go to Paris!     ->**Propose**

Alice&Ceris: Great! Let's go!  -> **Agree**

-> Then **Decide**



To describe a consensus problem: A distributed system consists of n processes : {0, 1, 2, 3, ..., n-1}. Each process has a value and they can communicate with each other. What we do is to design an algorithm that even some process crashes or fails, processes still decide on one single value and every execution satisfies these 4 properties: 

1. **C1. Validity**: Any value **decided** is a value proposed.
2. **C2.Agreement**: No two **correct** processes decide differently.
3. **C3.Termination**: Every correct process **eventually decides**.
4. **C4.Integrity**: No process decides twice.



### System Model

#### Network model

#### Failure Model

#### Message Model



