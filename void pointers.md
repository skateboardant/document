solve question raised in [why do void* pointers even exist?](https://www.youtube.com/watch?v=t7CUti_7d7c)



# Why do void* pointers even exist?

## 1. The role of void* pointers?

-  A void pointer is a pointer that has no associated data type. It simply points to a raw memory address but provides no information for computers about how much data is stored there or how it should be interpreted. It represents "typeless memory."
- So if you wanna deference a void* pointer variable, using *, there will be a compiler error "invalid use of void expression". The reason is the compiler is trying to make an assembly instruction to reach out to the address that's contained in our pointer variable and go get that many bytes but the problem is it's a void type it does not know how many bytes to get.

---

## 2. Dynamic memory allocation functions return a void* pointer

- When you ask the operating system for heap memory, it doesn't know what kind of data you plan to store there. Therefore, these functions return a void *—a raw, typeless block of memory. Because the C language is giving you the power to tell the compiler what type is stored there. 

- So, after using malloc for some heap memory space, you need to explictly cast it to the desired pointer type, which is called type cast.

- And then, if you want to later free it, free() function take a void* pointer as input, but the implicit cast here is actually no problem. 

    ---

## 3. Common pitfalls

- If we know the type of the memory ahead of time, we should never be using void star we should always include type information in our code as often as possible. 

- The minute that you know the type of that variable, you should then be casting it to that type or just use that type in general. 