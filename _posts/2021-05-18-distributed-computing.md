---
layout: post
title: Distributed Computing with PyTorch
date: 2020-02-19
use_math: true
---

## Some Definitions

#### 1. Process: 
An instance of the python. One process can be used to control on GPU. 
#### 2. Node: 
A node is the same as a computer with all of its resources.
#### 3. World-Size: 
Total number of the GPUs available. It is a product of total nodes and total GPUs per node. For example, if there are two servers and two GPUs per server, then the world size is 4. 
#### 4. Rank: 
It is an ID to identify a process among all the processes. For example, if we have two nodes (servers) with four GPUs each, the rank will vary from $0-7$. Rank $0$ will identify process $0$ and so on. 
#### 5. Local Rank: 
Rank is used to identify all the nodes, whereas the local rank is used to identify the local node. Rank can be considered as the global rank. For example, a process on node two can have rank two and local rank 0. This implies that among all the processes, it has rank 2, wheres on the local machine, it has rank 0. 


## Multiprocessing in PyTorch
Pytorch provides: 
``` python
torch.multiprocessing.spawn(fn, args=(), nprocs=1, join=True, daemon=False, start_method='spawn')
```
It is used to spawn the number of the processes given by "nprocs". These processes run "fn" with "args". This function can be used to train a model on each GPU. Let us take an example. Suppose we have a node (server) with two GPUs. We can create a function "fn()" to handle the training part. The "nprocs" will be equal to the two, the number of the GPUs.  Hence, "torch.multiprocessing.spawn" can be used to spawn the training function ("fn()") on each of the GPU through "args".  This can be done on each node (server). Next, we will see how these spawned processes will coordinate with each other.

## Distributed DataParallel (DDP)
In DDP the model is replicated on each GPU, and each GPU is handled by one process. DDP requires the following: number of nodes and number of GPUs in each node. We can have world size from this information. The training with DDP requires synchronization and communication among the processes. This is achieved through \textcolor{blue}{ distributed.init\_process\_group}. 
