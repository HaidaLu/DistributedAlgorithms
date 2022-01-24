

# DistributedAlgorithms

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

#### 1. Network model



<img src="figure/NetworkModel.png" style="zoom:50%;" />



##### - Synchronous

响应时间是在一个固定且已知的有限范围内.

##### - Asynchronous

响应时间是无限的.

在一个分布式异步系统中,即使只有一个进程出现了故障, 也没有算法能保证共识. 这是因为, 在异步系统中, 进程可以随时发出响应,所以没有办法分辨一个进程是速度很慢还是已经崩溃, 这不满足终止性Termination.

因此,分布式共识算法需要具有两个属性-> Safety 和 liveness.

Safety: Every correct process decides on one value.

Liveness: 分布式系统最终回认同某一个值.

因此,每个共识算法要么牺牲掉一个属性, 要么放宽对网络异步的假设.



**FLP Impossible**: 指无法确保达成共识, 并不是说如果有一个进程出错, 就永远无法达成共识. *这种不可能的结果来自于算法流程中最坏的结果*:

1. 一个完全异步的系统
2. 发生了故障
3. 最后, 不可能有一个确定的共识算法



针对这些最坏的情况, 可以找到一些方法, 尽可能去绕过FLP Impossible, 能满足大部分情况下都能达成共识,一下是常用的方法:

1. Fault masking
2. Failure detectors
3. Non-Determinism 随机性算法





#### 2. Failure Model

1. Fail-Stop Failures: 节点突然宕机并停止响应其他节点
2. Byzantine Failures: 源自拜占庭将军问题, 指节点响应的数据可能会产生无法预料的后果. 可能会相互矛盾或完全没有意义, 这个节点甚至在说谎, 比如黑客入侵的节点.

#### 3. Message Model

1. Oral Message: 消息在转述时可能被篡改
2. Signature Message: 消息被传出来后时无法被修改的, 一旦被篡改就会被发现.



## Consensus 

<img src="figure/2.png" style="zoom:50%;" />

### 1. (Regular) Consensus

#### (1) Property

1. **C1. Validity**: Any value **decided** is a value proposed.
2. **C2.Agreement**: No two **correct** processes decide differently.
3. **C3.Termination**: Every correct process **eventually decides**.
4. **C4.Integrity**: No process decides twice.



#### (2) Regular Consensus Fail-Stop Model Overview

- The processes exchange and update proposals in rounds and decide on the value of the non-suspected process with the smallest id.
- The processes go through rounds incrementally(1 to n): in each round, the process with the **id corresponding to that round is the leader** of the round.
- The leader of a round decides its current proposal and broadcasts it to all.
- A process that is not leader in a round waits(a) to deliver the proposal of the leader in that round to adopt it, or (b) to suspect the leader.

Summary: 

**Loop through rounds 1 to N, in round i**:

*1. process i is leader*:  broadcasts proposal v and decides proposal v.

*2. Other processes*: 

	- adopt i's proposal v and remember **currentProposal i**
	- Detect crash of i.



#### (3) Regular Consensus Fail-Stop Model Implementation

总结: 

**1. init** 初始化;

 **2. crash**: 检测故障; 

**3. Propose**: 如果没接受过别的proposal就自己propose一个;

**4. bebDeliver**: 接受该轮proposal; 

**5.已经deliver或是检测到crash**;

**6. Leader Propose**

<img src="figure/3.png" style="zoom:30%;" />

<img src="figure/4.png" style="zoom:30%;" />

​					*Set process's initial proposal, unless it has already adopted another node's*

<img src="figure/5.png" style="zoom:30%;" />

​	

​	***delivered[round] = true or Pround in suspected**: Next round if deliver or crash*

<img src="figure/6.png" style="zoom:30%;" />

​					Note: **- *Pround = self : If I am leader***

​								*- broadcast[round] = false -> trigger once per round*

​								*- currentProposal != nil: trigger if I have proposal*

​								*- trigger<Decide, currentProposal> : permanently decide.*

​						*如果我是leader, decide自己, 再将currentProposal beb给其他进程*

<img src="figure/7.png" style="zoom:30%;" />

<img src="figure/8.png" style="zoom:30%;" />



#### (4) Correctness argument

**How many failures it can tolerate?? -> N-1**

-> Basic idea: 

1. There must be a first correct leader P
2. P decides its value v and beb-casts v.
   1. every correct process adopts v 
   2. Future rounds will only propose v.



#### 上述算法或许可能出现的问题? (课程外)

<img src="figure/9.png" style="zoom:70%;" />

在第一轮decide a 后就crash了, 但是对于p3 它收到p1的a比收到p2还晚, 所以它在第三轮propose了a, 而p2在propse b后就decide了. 所以最后correct的p2 p3 decide differently.

解决方式: **Invariant: only adopt "newer" than what you have.**

<img src="figure/10.png" style="zoom:70%;" />



### 2. Uniform Consensus Algorithm II

#### (1) Property

1. C1. Validity: Any value decided is a value proposed.
2. **C2.Agreement: No two processes decide differently.**
3. C3.Termination: Every correct process eventually decides
4. C4.Integrity: No process decides twice.



#### (2) Overview

- The processes go through rounds incrementally (1 to n): in each round I, process PI sends its currentProposal to all.
- A process adopts anty currentProposal it receives.
- Processes decide on their currentProposal values **at the end of round n.**



#### (3) Implementation

总结: 与Regular Consensus 的不同之处

(1): 多了一个变量**decided** 在init event中赋值为false

(2): 已经deliver或是检测到crash: 如果已经到第n轮并且还没decided 那就decide, 否则round + 1;

(3): If I am leader: 这个阶段不需要decide自己, 只需要将当前值beb给其他所有processes

<img src="figure/11.png" style="zoom:70%;" />

#### (4) Correctness Argument

##### A. 

**Lemma: If a process pJ completes round I without receiving any message from pI and J > I, then pI crashes by the end of round J.**

Proof(?): 假设J > I, J 首先完成I 轮但是没有收到pI 的消息, 而pI又completes round J.  那么pJ 会在round I suspect pI.

因此, pI has crashed before pJ completes round I. 

所以在第J 轮, 要么 pI suspects pJ (不可能, 因为pI 已经crash了) 要么 pI receives round J message (也不可能因为I 已经crash了)

##### B. Uniform Agreement

Consider the process with the lowest id which decides, say pI. Thus, pI completes round n. By previous lemma, in round I, every pJ with J > I receives the currentProposal of pI and adopts it. Thus, every process which sends a message after round I or decides, has the same currentProposal at the end of round I.



### 3. Uniform Consensus Algorithm III

#### (1). Overview

- A uniform consensus algorithm assuming: 
  - A correct majority
  - a <>P failure detector
- Basic idea: the processes alternate in the role of a phase coordinator until one of them succeeds in imposing a decision.
- <>P ensures: 
  - **Strong completeness**: Eventually every process that crashes is permanently suspected by all correct processes.
  - **Eventual strong accuracy**: Eventually no correct process is suspected by any process.