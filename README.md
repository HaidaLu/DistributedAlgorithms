

# DistributedAlgorithms

#Consensus

In the consensus problem, the processes propose values(Here we consider Single value consensus) and have to agree on one among these values.



Solving Consensus is key to solving:

1. Total order broadcast(aka Atomic broadcast)

2. Atomic commit(in database)

3. Terminating reliable broadcast: The process knows the broadcast has been terminated.

4. Dynamic group membership: The number and identity of the process in a group can be changed overtime and they have to agree who is in the group and who is not.

5. Stronger shared store models.

   