---
layout: post
title: Distributed Computing with PyTorch
date: 2021-05-18
use_math: true
---

We will discuss two techniques that can be used for distributed computing with PyTorch. Let's discuss some definitions first. 

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
In DDP the model is replicated on each GPU, and each GPU is handled by one process. DDP requires the following: number of nodes and number of GPUs in each node. We can have world size from this information. The training with DDP requires synchronization and communication among the processes. This is achieved through  distributed.init\_process\_group. 
``` python
os.environ['MASTER_ADDR'] = '19.16.19.19'
os.environ['MASTER_PORT'] = '8888'
dist.init_process_group(backend='nccl', init_method='env://', world_size=world_size, rank=rank)
```
init\_process\_group requires the details of the master. These have been set as environment variables. It is used for the synchronization of all the processes. It also requires the world\_size as well as the rank (not the local rank) of the process. We can pass all this information as "args" in "torch.multiprocessing.spawn". The rank can be found as: "node\_rank$\times$ gpus\_per\_node + local\_rank". Note that local rank will vary from 0-nprocs.
## DistributedSampler
In DDP each process is handling one GPU, and each GPU uses an exclusive chunk of the dataset to train the dataset. For example, if we have two GPUs and 100 training samples, and a batch size of 50, then each GPU will be using 50 non-overlapping training samples. This is achieved through "DistributedSampler" provided by the PyTorch. It makes sure each GPU is using an exclusive subset of the dataset. It requires world\_size and rank (global rank) of the process. 

## The Whole Picture
Let's combine everything to use distributed computing in the PyTorch. First, we will write the function to get the data loader. 

``` python
def get_dataset(world_size, global_rank, batch_size):

    transform_train = transforms.Compose([
    transforms.Resize((224,224)),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.456, 0.456, 0.456], std=[0.224, 0.224, 0.224])])

    train_dataset = torchvision.datasets.CIFAR10(root='./data', train=True, transform=transform_train, download=True)


    train_sampler=torch.utils.data.distributed.DistributedSampler(train_dataset,\
    num_replicas=world_size,rank=global_rank)

    train_loader = torch.utils.data.DataLoader(dataset=train_dataset, \
        batch_size=batch_size, shuffle=False, num_workers=0, pin_memory=True,\
        sampler=train_sampler)

    return train_loader
```
We are using train\_sampler to pass unique samples to each GPU. DataLoader now also takes train\_sampler along with train\_dataset. 

Next, write a function to train the model:

``` python
def train(local_rank, args):
    ## global_rank is the global rank of the process (among all the GPUs (not just on a particular node). )
    global_rank = args.node_rank * args.gpus + local_rank
    dist.init_process_group(backend='nccl', init_method='env://', world_size=args.world_size, rank=global_rank)
    torch.manual_seed(0)
    torch.cuda.set_device(local_rank)
   
    batch_size = 64

    criterion = nn.CrossEntropyLoss().cuda(local_rank)

    model=models.resnet18(pretrained=True)
    model.fc=nn.Linear(512, 10)
    model.cuda(local_rank)
    model = nn.parallel.DistributedDataParallel(model, device_ids=[local_rank])

    optimizer = torch.optim.SGD(model.parameters(), lr=1e-3)

    train_loader=get_dataset(args.world_size, global_rank, batch_size)
    
    for epoch in range(10):
        for i, (images, labels) in enumerate(train_loader):
            images = images.cuda(non_blocking=True)
            labels = labels.cuda(non_blocking=True)
            # Forward pass
            outputs = model(images)
            loss = criterion(outputs, labels)

            # Backward and optimize
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            ## if this condition is not used, output will print for each process (GPU)
            if local_rank == 0:
                print('Epoch [{}/{}],Loss: {}'.format(epoch + 1, 10,loss.item()))
```

Finally, use multiprocessing to spawn the train() function.

``` python
parser = argparse.ArgumentParser()
parser.add_argument('--total_nodes', default=1, type=int, help='total number of the nodes')
parser.add_argument('--gpus', default=1, type=int, help='number of gpus per node')
parser.add_argument('--node_rank', default=0, type=int, help='rank of present node (server).')
args = parser.parse_args()


args.world_size = args.gpus * args.total_nodes
os.environ['MASTER_ADDR'] = '19.16.19.19'
os.environ['MASTER_PORT'] = '8888'
mp.spawn(train, nprocs=args.gpus, args=(args,))
```

We can then use DDP by running the script on each node wityh suitable argument. For example, if we have twp nodes each with 4 GPUs; the arguments for node (server) '0' will be:

 --total\_nodes 2 --gpus 4 --node\_rank 0
 
 Similarly, for node 1:
 
  --total\_nodes 2 --gpus 4 --node\_rank 1
  
Note that dataset has to be present on each node. 

## The Easy Way

The above methodology can be simplified drastically by using "torch.distributed.launch". It will take care of the spawning processes. We only have to parse the command-line argument:
"--local\_rank", and the rest will be handled by this module. The modified script is given as:


``` python
parser = argparse.ArgumentParser()
    parser.add_argument("--local_rank", type=int, default=-1, help="local_rank for distributed training on gpus")
    args = parser.parse_args()

    os.environ['MASTER_ADDR'] = '19.16.19.19'
    os.environ['MASTER_PORT'] = '8888'

    dist.init_process_group(backend='nccl', init_method='env://')
    torch.manual_seed(0)

    torch.cuda.set_device(args.local_rank)

    model=models.resnet18(pretrained=True)
    model.fc=nn.Linear(512, 10)
    model.cuda(args.local_rank)
    model = nn.parallel.DistributedDataParallel(model, device_ids=[args.local_rank])


    batch_size = 64
    criterion = nn.CrossEntropyLoss().cuda(args.local_rank)
    optimizer = torch.optim.SGD(model.parameters(), lr=1e-3)
    train_loader=get_dataset( batch_size)




    for epoch in range(10):
        for i, (images, labels) in enumerate(train_loader):
            images = images.cuda(non_blocking=True)
            labels = labels.cuda(non_blocking=True)
            # Forward pass
            outputs = model(images)
            loss = criterion(outputs, labels)

            # Backward and optimize
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            ## if this condition is not used, output will print for each process (GPU)
            if args.local_rank == 0:
                print('Epoch [{}/{}],Loss: {}'.format(epoch + 1, 10,loss.item()))
```
As we can see, this script is much simpler. On node 0, this script can be used as

``` python
python -m torch.distributed.launch --nnodes 2  --node_rank 0 --nproc_per_node=2 train_distributed_2.py
```
On node 1
``` python
python -m torch.distributed.launch --nnodes 2  --node_rank 1 --nproc_per_node=2 train_distributed_2.py
```
Similarly, for other nodes also. Master address and can also passed as arguments with this module.
