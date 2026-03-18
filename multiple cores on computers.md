solve question raised in [why can’t computers have thousands of cores?](https://www.youtube.com/watch?v=dMejriBC_Kc)



# Why can't comoputers have thousands of cores?

## 1. The definition of the CPU core

- To able to answer the question "why can't computers have thousands of cores?", we first need to find what a CPU core is.

- The computer processors actually doo is to run code. And the processors serveral basic things:

    1. Internal memory(registers)
    2. buffer memory(cache), acts as a buffer beteen the internal memory and RAM.
    3. some additional hardware to interface between the CPU and the motherboard.
    4. an execution engine

    

- The extremely fast efficient loop CPU runs

1. Fetch (fetch instructions from memory)
2. Decode (convert instructions into internal micro instructions and logic)
3. Execute (execute the task)

- Once the execution is complete, it will then store the result of that operation into the desination.
- The piece of hardware responsible for the task is referred as a core. So, <u>one CPU is able to perform one task execution (program)</u>.

---

## 2. How to schedule multi-core processor

- When a multi-core boots  at the very beginning, only one core is active. Once the first core in program memory, software then activates the extra cores. Eventually, the operating system boots up all cores. 

### I. Processor Affinity

- The code prefers and is more efficient to be ran on the core that started running. 
- Primarily due to the way that CPU cache works. Cache is filled with data related to the code ran on the core.
-  Leaving a piece of code running on a core coomes at a cost though. If the code is blocking the processors on an input event or waiting for an interrupt, it may be advantageous to forgo the cost of violating affinity to make sure the core is being effectively used and not blocking. 

### II. Asymettric Multiproocessing

- One of cores is the boss of other corer, running the kernel code and hence the scheduler. Other cores take orders from the scheduler and only ever run user space code. 
- It is easier to implement, but less efficient. 

### III. Symettric Multiprocessing

- Every processor is just a worker that is free to execute code as needed. And the work is derived from some global work queue where tasks are pushed and poped as requirements for execution to rise. 
- It is harder to implement, but more efficient.

---

## 3. Why not more cores?

- Some processors these days actually do have more cores, but this core density is a result of some insame material and semiconductoor engineering that have to consider first some difficult constrains. 

1.  Thermal Efficiency

- As you pack more silicon into tighter space, making it more thermally dense. The efficiency and performance of those cores go down. Ultimately, at a cost to the performance that outweights that gain from adding a core. 

2. Memory Bottlenecks

- While the memory internal to a proocessor is extremely fast via the cache or the registers themselves, it is ultimately bogged down by its ability to write back memory to RAM, which is limited by the speed of the RAM bus and its size of its access bus.
- At a certain number of cores, there's so much blocking on memory accessed that eventually no one can get any work down and ultimately the additional of cores is not worth the cost. 

3. Bad Code

- Some code just doesn't parallelize well. 



- Ultimately, it's actually on the programmer to make use of all threads of execution available on the processor and to write code in a way that it scales up linearly with the number of cores available. 