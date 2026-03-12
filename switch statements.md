solve question raised in [why are switch statements so HECKIN fast?](https://www.youtube.com/watch?v=fjUG_y5ZaL4)



# Why are switch statements so HECKIN fast?

- When making desicions with data in C, you basically have two options for the majority of the control flow, if statements and switch statements. 

## 1. if statements under the hood

- An if / else if chain is fundamentally a **Linear Search**. The compiler translates this into a sequence of **Compare and Branch** instructions.

**How it works in Assembly:**

1) **Compare (cmp):** The CPU compares a variable against a specific condition.
2) **Conditional Jump (jcc):** The CPU checks the result. If the condition is *false*, it physically jumps over the current block of code to the next if statement.
3) **Repeat:** It does this sequentially until it finds a true condition or hits the final else.

- So, the time-complexity of if / else if statementis O(N) linear time.

---

## 2. switch statements under the hood

- When the compiler creates a switch statement, it builds a hidden array in memory (the jump table) containing the destination addresses for every case. Here is exactly how the assembly navigates it:

### Step 1: Normalization (Creating a Zero-Index)

- You can't use random keyboard characters (like 'c', 'q', 'e') directly as array indexes. The CPU must convert the user's input into a clean 0, 1, 2, 3... index.
- The CPU takes the lowest ASCII value from your cases. In the video, for example, the lowest value is the character 'c' (which is hex 0x63).
- The instruction sub eax, 0x63 subtracts 0x63 from whatever the user typed.

### Step 2: The Boundary Check (The Safety Net)

- Because we are using user input to access a hidden memory array, the CPU must ensure the user didn't type a letter that will cause the program to read memory out of bounds.
- The instruction cmp eax, 0xe compares our new normalized index against the maximum size of our table (in this case, hex 0xE, which is 14 in decimal).
- Then, ja (Jump if Above) is called.
- If the normalized index is greater than 14, the CPU instantly teleports to the default: or break; case, preventing a crash.

### Step 3: The Jump Table Lookup (The Magic Math)

- Now that the CPU has a safe index, it needs to look up where the code for that case lives.

-   The CPU looks at the memory address where the hidden Jump Table starts (In the video, this base address is 0x2054).

- The CPU calculates the exact slot in the table using the formula: Base Address + (Index * 4). (It multiplies by 4 because the addresses are stored as 32-bit/4-byte blocks). Then, the CPU reads the 4 bytes at that exact location (In the video, the bytes read are ff ff f2 42).

### Step 4: Decoding the Offset and Teleporting

- This is the part that blows people's minds. The bytes ff ff f2 42 are not a direct memory address; they are a **relative offset** (directions on how far to jump).

- Because x86 processors use **Little Endian** architecture, bytes are read in reverse order. So, ff ff f2 42 is actually read by the computer as 0xfffff242.
- In binary math (Two's Complement), starting a number with a bunch of fs means it is a **negative number**. 0xfffff242 translates to -0xdbe.
- The CPU takes the Jump Table's base address (0x2054) and adds this negative offset (-0xdbe) to it (0x2054 - 0xdbe = 0x1296). So, 0x1296 is the exact memory address where the code for handleContinue() lives. The CPU fires a single jmp (jump) instruction to teleport directly to 0x1296.

- As a result, the time-complexity of switch statement is O(1), which is much fast than as the options get larger. 

---

​	By understanding this, you can see why switch statements are so powerful for system performance, Instead of wasting CPU cycles executing dozens of cmp (compare) and jne (jump-if-not-equal) instructions like an if/else tree.