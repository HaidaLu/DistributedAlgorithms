## Overview

Like reliable broadcast, terminating reliable broadcast(TRB) is a communication primitive used to disseminate a message among a set of processes in a reliable way.

TRB is however strictly stronger than (uniform) reliable broadcast.



- Like with reliable broadcast, correct processes in TRB agree on the set of messages they deliver
- Like with (uniform) reliable broadcast, every correct process in TRB delivers every message delivered by any process.
- Unlike with reliable broadcast, every correct process delivers a message, even if the broadcaster crashes.



## Problem Description

- The problem is defined for a specific broadcaster process pi = src(known by all processes)
- Process src is supposed to broadcast a message m(distinct from $\varphi$)