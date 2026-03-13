solve question raised in [do you know how "return" works under the hood? (are you SURE?)](https://www.youtube.com/watch?v=e46wHUjNDjE)



# How "return" works under the hood?

## 1. Function()

- Typically when we write code, we organize them into blocks that all should perform one singular action. These blocks are called **functions**. 
- We use functions, because we don't want to repeat the same logic flow mutiple times. At the software aspect standard, this is called "Don't Repeat Yourself". 

###  Fundamental Concepts of Functions

- Callee (Function): A sequence of program instructions that performs a specifici, singular task.
- Caller: The part of the code that invokes or triggers the function.
- Arguments (optional: The data passed into the function by the caller.
- Return Value (optional): The result or output produced by the function that is sent back to the caller.

---

## 2. The Core Problem: Where to Return?

- But, how does a program know where to return to when the callee ends, how does the return statement return to the right location. 

### A. **Failed Solution 1 (Hardcoding):**

- You cannot calculate the return location at compile time and hardcode it into the machine code. A single function can be called from multiple different places in a program, so it needs to return to multiple different locations.

### B. **Failed Solution 2 (Lookup Tables)**

- Creating a map that links callers to return addresses doesn't work for "computed calls" (like dynamic dispatch in C++). In these cases, the function being called—and thus the return address—isn't determined until the program is actually running (runtime).

---

## 3. Hardware Architecture

- The solution to the return problem is actually not based on how the return statements, but instead on how the function is called. It relies on how the CPU and memory interact. So, let's dive into hardware architecture level. 
- Inside the CPU through variables called registers. They are extremely fast, temporary storage variables located directly inside the CPU. 
    - **PC (Program Counter) / IP (Instruction Pointer)**: Constantly stores the memory address of the *very next* instruction the CPU needs to execute.
    - **SP (Stack Pointer)**: Points to the current "top" of the stack in memory.
    - **BP (Base Pointer):** Points to the bottom (or base) of the current active stack frame.
- **The Stack:** A special region of RAM (Random Access Memory) used by the program to store temporary variables, runtime information, and, crucially, return addresses.

---

## 4. The Step-by-Step Execution in Assembly Level

- When the caller invokes a function, the compiler translates it into an assembly instruction like call instruction in Intel (x86) for example. 
- The callee allocate its stack frame to store temporary variables, runtime information, and, crucially, return addresses. The stack frame is bounded by the Base Pointer (BP) and the Stack Pointer (SP).Then the callee secretly saves the value of program counter (return address) onto that stack fram. Next, the set the pc to the start address of the function and execute. 
- During the execution, the stack fram can be adjust for local variables temporarily storage. 
- When the pc comes to the end of the function execution, where the return statement is invoked. Before the function returns, it collapses its stack frame. So, the sp pointer to the previously saved return address. 
- In Intel (x86) assembly, convention dictates that the return value is placed in the 'A' register (ax, eax, or rax, depending on the data size). Before executing the ret instruction, the program will load the result into this register. The caller then knows to look inside rax to find the answer.
- In assembly, the return statement gets boiled down to a single instruction **ret**. The ret instruction pops off the return address into the pc, and CPU continues execution after the call. 