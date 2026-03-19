solve question raised in [how does a Mutex even work? (atoms in the computer??)](https://www.youtube.com/watch?v=1tZhmTnk-vcv)



# How does a Mutex even work?

## 1. task synchronization

- Task synchronizetion makes multiple thread play nice. It synchronizes the execution of multiple tasks or threads that use the same global data to prevent data corruption or misinterpretation. 
- Standard processors process memory or threads in three separate steps:
    1. **Read** the variable from memory into a CPU register.
    2. **Modify** the value.
    3. **Write** the new value back to memory.
- For instance, Task1 may be interrupted at the middle of Modify operation by Task2. After Task2 modify the same global variable and return control back to Task1, the actual global and the value Task1 holds can be different. This is a classic **race condition**.

- The task synchronization fix the race condition problem. And an easy way to do task synchronization is using a mutex or a mutual exclusion object. 
- In C, the way to use mutex is as follows:
    1. `#include<pthread>`
    2. declare a mutex structure globally, and initialize it with `pthread_mutex_init()`function.
    3. If you wanna protect the critical section to be only executed by a thread at a time, lock that thread using the `pthread_mutex_lock()`function, then use the `pthread_mutex_unlock()`function to unlock it.

---

## 2. how to implement the mutex at low level?

- Doesn't multiple threads trying to access a single mutex also creates a race condition? Couldn't two threads accidentally acquire the same mutex in a race condition at the same time?

- This is where the idea of atomic operating comes in. Atomic operations are instructions within the processor that cannot be interrupted. 
- When a processor executes an atomic instruction, it locks the memory bus or the specific cache line holding that variable. It performs the *Read*, *Modify*, and *Write* phases as a single, microscopic, unbreakable unit. It becomes physically impossible for the operating system to interrupt the thread mid-check, and it is physically impossible for another core to access that memory address until the instruction is completely finished.
- The mutex is just an integer value, 0 or 1, indicates to the thread if the lock has been acquired or not. The mutex is created in shared memory as a 0.
- In Intel assembly for example, the lock prefix tell the processor that to execute instruction atomically meaning no one is able to access the memory targeted by the  instruction until the instruction completes execution. 

### I. Lock the mutex

- We need to implement the mutex acquiring by the help of cas (compare and swap), which is an **atomic instruction** provided by the hardware processor.

``` assembly
lock_acquire:
  li $t0, 0          # Load Immediate: Set register $t0 to 0 (Unlocked)
  li $t1, 1          # Load Immediate: Set register $t1 to 1 (Locked)
  cas $t0, $t1, lock # Compare And Swap
  beq $t0, $t1, lock_acquire # Branch if Equal
```

**Break down the assembly code**:

`cas $t0, $t1, lock`: 

- It looks at the memory address labeled `lock`.

- It compares the value currently in `lock` to the value in `$t0` (which we set to 0).

- **If lock == 0 (it is unlocked):** The processor swaps the value in `lock` with the value in `$t1` (changing the lock to 1).
- **If lock != 0 (it is already locked):** It does not modify the `lock`.
- **The Crucial Step:** Regardless of whether it succeeds or fails, the cas instruction overwrites register `$t0` with the ***old*** value it found inside `lock`.

`beq $t0, $t1, lock_acquire`:

- beq evaluates the results of the previous step. It compares our newly updated `$t0` (the original state of the lock) with `$t1` (which is 1).
- **If $t0 == 1:** This means the lock was *already locked* when our thread checked it. Because they are equal, the instruction branches (jumps) back up to the `lock_acquire` label. This forces the processor to loop over and over, constantly checking the lock until it becomes free. This is known as a "spinlock."
- **If $t0 == 0:** This means the lock was free, and our cas instruction successfully changed it to 1. The processor does *not* jump back to the top. Instead, it "falls through" to the next line of code, allowing the thread to enter the critical section of your program safely.



### II. unlock the mutex

``` assembly
lock_release:
  mfence             # MEMORY FENCE: Serialize all memory operations. Do not 
                     # execute the next line until global memory is fully updated.
                     
  mov dword [rax], 0 # MOVE: Write the value '0' into the memory address 
                     # held in the rax register (the lock).
```

**CPU Instruction reordering**:

- To maxmize speed, modern CPU architecture introduces a massive hazard: **Out-of-Order Execution**, and does not execute instructions strictly in the order you wrote them. If a processor sees that instruction #4 doesn't depend on instructions #1, #2, or #3, it might execute #4 first. Furthermore, processors write data to their own local, blazing-fast hardware caches before syncing that data to the computer's slower main memory (RAM).
- Imagine this scenario inside your critical section:
    1. Thread A acquires the lock.
    2. Thread A updates shared variable X to 100 (this update sits in Thread A's local CPU cache).
    3. Thread A releases the lock by writing 0 to the lock variable.
    4. Because the CPU reordered the memory writes, the lock variable becomes 0 in main memory **before** X is synced to main memory.
    5. Thread B instantly grabs the newly freed lock, reads X, and gets the old, corrupted data!

**Memory Fence(Barriers)**:

- To prevent out-of-order execution from destroying the integrity of a mutex, the release operation must implement a **Memory Fence** (also called a Memory Barrier).
- A memory fence is a special hardware instruction (like mfence on x86 processors or sync on MIPS/ARM). It tells the processor: *"Do not execute any further instructions, and do not let any other threads see the new lock value, until absolutely all memory modifications made prior to this line are fully written to main memory."*
- So, a true, safe release operation under the hood actually looks like this:
    1. **Memory Fence:** Flush all local CPU cache changes to global memory so all other threads can see the updated shared data.
    2. **Store:** Write 0 to the mutex variable to unlock it, allowing other looping threads to acquire the lock and enter the critical section. 