solve question raised in [why is recursion bad?](https://www.youtube.com/watch?v=mMEmNX6aW_k)



## Why is recursion bad?

- On limited memory space system, recursion is highly discouraged. The reason lies on how a function call works at low level. 
- When a function is called, under the hood, in the assembly, the processor is allocating memory space to store local variables for that function. These local variables live in, what is called the stack frame. This stack frame has enough room to contain every local variable that function may need. 
- If you do logic inside the function using recursion, that's where things get out of control. Every time we recurse, the stack frame of the called function get reallocated, occupying another copy of the stack frame space. Even though some of these local variables may not get used in the recursion, the processor is still instructed to occupy sapce for them. If your recursion goes to long, it's 100% that you eat up all the memory on the processor with the stack frame and eventaully crash it. 

**Should we throw the recursion out of the window?**    --  no, of course not. 

- But we'd better make recursive stage safer with some easy safeguards. The biggest one is to set a max recursion depth by checking how deep your recursion is with every call. You can limit the amount of memory your recursion logic uses by setting a max depth. 



- So, if you are programming on a system with gigabits and gigabits of RAM, then have at it recurse to your heart's content. However, If you are an embedded system developer, be wary of the next time, you calculate thousandth recursive result.