solve question listed in [computers suck at division](https://www.youtube.com/watch?v=ssDBqQ5f5_0)

# Why do computers suck at division?

## 1.  The Hardware Constraints (The Problem Space)

### A. **No Floating Point Unit (FPU)**

- Some  Low-End ARM processors (like the Cortex-M0 or M1) cannot natively understand 0.555. It only understands integers (1, 50, 1024). So, doing math like 5 / 9 in integer arithmetic results in 0. The fractional data is immediately lost.

### B. **No Hardware Division Unit**

- Division is the most expensive arithmetic operation in terms of silicon die space and clock cycles (it is an iterative process, unlike addition which is parallel). Older or cheaper chips (Cortex-M0) physically lack the circuitry to perform division. If you try to run a divide instruction, the CPU will throw a "Hard Fault" or undefined instruction error.

---

## 2.  The Mathematical Theory (Fixed-Point Arithmetic)

- To solve the hardware constraints, we must convert a division problem into a multiplication problem using **Fixed-Point Arithmetic**.

### A. The transformation

- We need to calculate: $$ result = input  ÷  9$$
- This is mathematically identical to: $$result = input * (1/9) $$

### B. Scaling Factor (Q-Format)

- The processor in the video acts like a calculator that has no "dot" button. It can only process whole integers (1, 50, 1000). It cannot process 0.111.


#### I. The Analogy (Dollars vs. Cents)

Imagine you need to calculate **$1.50 ÷ 2** using a calculator that has no decimal point.

- **Step 1 (Scale Up):** Multiply $1.50 by 100. Now you have **150 cents** (a whole integer).
- **Step 2 (Do the Math):** 150 ÷ 2 = **75**.
- **Step 3 (Scale Down):** You know the "dot" is implicitly 2 digits to the right. To get the real answer in dollars, you mentally shift the decimal back: **$0.75**.

#### II. The Binary Version

- The computer does the exact same thing, but instead of scaling by 10 or 100, it scales by powers of 2 (specifically 2^32^ in this case) because computers speak binary.
- 1/9 is roughly **0.1111...** (The computer cannot hold this).
- So, we perform the **Scaling**:
    - represent the number as a binary fixed point number, and approximate the value to a certain point by truncating the pattern.
    - Shift the decimal point 32 spots to the right (Multiply by 2^32^).
    - The result is the "Magic Number": **477,218,589** (roughly).
- When we multiply our input (Temperature) by this magic number, the result is huge. It is **32 bits too big**. To get the actual answer, we must *"**undo**" the scaling by shifting the decimal point 32 times back to the left (dividing by 2^32^).*

---

## 3. The Assembly Implementation (The Mechanism)

**The Critical Instruction:** **smull**, which explains why the answer lands in r0 and why r4 is ignored.

#### A. The Multiplication Problem

- The input (Temperature) is a **32-bit** number, and the Magic Constant is also a **32-bit** number. When you multiply two 32-bit numbers, the maximum possible result is **64 bits***(Think about base 10: $$ 99×99=9801$$. Two 2-digit numbers make a 4-digit number).

#### B. The Hardware Limitation

- The ARM processor in the video is a **32-bit architecture**. This means a single register (like r0 or r4) can only hold 32 bits of data. It literally cannot fit the 64-bit result of the multiplication.

#### C. The Solution: smull

- The assembly instruction used is <u>smull (Signed Multiply Long)</u>. This instruction is designed specifically to solve this problem. It takes two 32-bit inputs and spreads the result across **two** target registers.

- The syntax is:   smull [Low_Dest], [High_Dest], [Input_A], [Input_B]

---

## 4. The "Magic" Synthesis(core optimization)

- To "undo" the scaling we did, we must divide by 2^32^.
- In binary, dividing by 2^32^ is a Right Shift of 32.
- If you have a 64-bit number spread across two registers (High and Low), and you want to shift it right by 32 bits:
    - The "Low" 32 bits (r4) fall off the edge and disappear (these represent the fractional decimal dust we don't care about).
    - The "High" 32 bits (r0) slide down into the "Low" position.
- **Therefore, we don't actually have to perform a division instruction.** By simply grabbing the value inside the **High Register (r0)** and ignoring the Low Register (r4), we have effectively divided by 2^32^ for free.
- *Optimization:* We avoided a division instruction and we avoided a separate bit-shifting instruction. The smull did both at once.

---

## 5. Why doesn't ARM just include a divider?

### A. **RISC Philosophy (Reduced Instruction Set Computer)**

- Arm architecture belongs to RISC，the goal of RISC is to keep the hardware simple and fast. So, If an operation (like division) is rare or can be emulated by software (using multiplication), chip designers often remove it to save power and cost.

### B. **Throughput vs. Latency**

- **Hardware Division:** Might take 12–50 clock cycles to complete.
- **Multiplication:** Usually takes 1–3 clock cycles.
- By using the "Magic Constant" method, the programmer can perform division in roughly 3 cycles (loading the constant + multiplication), which is significantly faster than even a dedicated hardware divider.
