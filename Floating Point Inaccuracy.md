solved question raised in [coding in c until I go completely insane](https://www.youtube.com/watch?v=TQDHGswF67Q)

# Why 0.1 + 0.2 != 0.3

First let me explain details of **IEEE 754 Floating Point Standard**:

### 1. The Structure (The 3 Components)
To store a number in computer memory, IEEE 754 divides the available bits into three distinct sections.

#### A. The Sign Bit ($S$)
*   **Length:** 1 bit.
*   **Function:** Determines if the number is positive or negative.
*   **Values:**
    *   `0` = Positive
    *   `1` = Negative

#### B. The Exponent ($E$)
*   **Length:** 8 bits (Single Precision) or 11 bits (Double Precision).
*   **Function:** Determines the magnitude (how big or small the number is) by shifting the decimal point.
*   **The Bias:** Because the exponent needs to represent both positive powers (large numbers) and negative powers (tiny decimals), it uses a "bias." instead of a sign bit.
    *   For Single Precision, the bias is **127**.
    *   For Double Precision, the bias is **1023**.
    *   *Example:* If you want an exponent of $2^3$, you store $3 + 127 = 130$ in binary. If you want $2^{-3}$, you store $-3 + 127 = 124$.

#### C. The Mantissa (or Significand) ($M$)
*   **Length:** 23 bits (Single) or 52 bits (Double).
*   **Function:** Holds the actual digits (precision) of the number.
*   **The "Hidden Bit":** In binary scientific notation, the first digit is *always* 1 (because if it were 0, you would shift the exponent until it became 1).
    *   Example: `0.00101` becomes $1.01 \times 2^{-3}$.
    *   Since the leading `1` is constant, IEEE 754 **does not store it** to save space. It is implicit.

---

### 2. The Formats
There are two primary formats defined by the standard.

| Feature       | **Single Precision (32-bit)**                | **Double Precision (64-bit)**       |
| :------------ | :------------------------------------------- | :---------------------------------- |
| **Use Case**  | Graphics (GPUs), legacy systems, low memory. | Default for Python, JS, C `double`. |
| **Sign Bit**  | 1 bit                                        | 1 bit                               |
| **Exponent**  | 8 bits                                       | 11 bits                             |
| **Mantissa**  | 23 bits                                      | 52 bits                             |
| **Precision** | ~7 decimal digits                            | ~15-17 decimal digits               |
| **Bias**      | 127                                          | 1023                                |

---

### 3. The Mathematical Formula
To translate the binary bits back into a real decimal number, the computer uses this formula:

$$ \text{Value} = (-1)^S \times (1 + M) \times 2^{(E - \text{Bias})} $$

1.  **$(-1)^S$**: If S is 0, this is $+1$. If S is 1, this is $-1$.
2.  **$(1 + M)$**: This adds the "Hidden Bit" (the implicit 1) back to the stored fraction.
3.  **$2^{(E - \text{Bias})}$**: This un-biases the exponent to find the actual power of 2.

---

### 4. Step-by-Step Conversion Example
Let's convert the number **-13.5** into **Single Precision (32-bit)** binary.

1.  **Sign:** It is negative, so $S = 1$.
2.  **Binary Conversion:**
    *   Integer 13 is `1101` in binary.
    *   Decimal 0.5 is `0.1` in binary ($1/2$).
    *   Combined: `1101.1`
3.  **Normalize (Scientific Notation):**
    *   Shift the decimal point left until there is only one 1 before it.
    *   $1.1011 \times 2^3$
4.  **Calculate Exponent:**
    *   The power is 3. Add the bias (127).
    *   $3 + 127 = 130$.
    *   130 in binary is `10000010`.
5.  **Calculate Mantissa:**
    *   Take the normalized digits after the dot: `1011`.
    *   Drop the leading 1 (it's hidden).
    *   Fill the rest of the 23 bits with zeros: `10110000000000000000000`.

**Final 32-bit Storage:**
`1` `10000010` `10110000000000000000000`

---

### 5. Special Cases (Exceptions)
IEEE 754 reserves specific Exponent patterns to represent values that aren't standard numbers.

| Exponent   | Mantissa     | Meaning       | Description                                                  |
| :--------- | :----------- | :------------ | :----------------------------------------------------------- |
| **All 0s** | **0**        | **Zero**      | Can be +0 or -0 depending on the sign bit.                   |
| **All 0s** | **Non-Zero** | **Subnormal** | Extremely small numbers close to zero (precision is lost here). |
| **All 1s** | **0**        | **Infinity**  | Result of dividing by zero or overflow. Can be $+\infty$ or $-\infty$. |
| **All 1s** | **Non-Zero** | **NaN**       | "Not a Number". Result of invalid operations like $\sqrt{-1}$ or $0/0$. |

### 6. Why `0.1 + 0.2 != 0.3` Happens
#### 1. The Translation Problem (Base 10 vs. Base 2)
Humans calculate in **Base 10** (decimal), but computers store data in **Base 2** (binary).
*   In Base 10, a number like $1/3$ ($0.333...$) repeats infinitely and cannot be written down perfectly.
*   In Base 2, fractions are represented by powers of 2 ($1/2$, $1/4$, $1/8$, $1/16$, etc.).

**The conflict:**
The number $0.1$ (which is $1/10$) cannot be represented perfectly in binary. It creates an infinitely repeating binary fraction:
$$0.00011001100110011...$$

Because the computer cannot store an infinite string of numbers, it must cut it off (truncate) at a specific point.

#### 2. The Storage Limitations (Precision)
In the Python/JavaScript examples shown in the video, the computer uses **Double Precision** (64-bit) floating-point format.
*   **1 bit** for the sign (+/-).
*   **11 bits** for the exponent.
*   **52 bits** for the significand (the actual digits of the number).

When the computer stores $0.1$, it fills those 52 bits and then has to round the remaining infinite sequence. This results in a number that is **ever-so-slightly larger** than the actual $0.1$.

**Actual stored values:**
*   **0.1** is stored as: `0.100000000000000005551115123126...`
*   **0.2** is stored as: `0.200000000000000011102230246252...`

#### 3. The Addition (Compounding Errors)
When the computer executes `0.1 + 0.2`, it adds these two slightly inaccurate stored values together.

*   Stored `0.1`: `0.10000000000000000555...`
*   Stored `0.2`: `0.20000000000000001110...`
*   **The Sum:** `0.30000000000000001665...`

The computer then takes this sum and fits it *back* into the 64-bit storage format. The closest representable binary number to that sum is:
**`0.30000000000000004`**

#### 4. The Comparison Failure
Now the computer compares your calculation against the hardcoded number `0.3`.

When you type `0.3` directly into code, the computer also converts that to binary and rounds it. However, the rounding error for `0.3` calculated directly is different than the rounding error of `0.1` plus `0.2`.

*   **Left side (`0.1 + 0.2`):** `0.3000000000000000444...`
*   **Right side (`0.3`):** `0.2999999999999999888...`

The two binary patterns are not identical, so the boolean check returns `False`.

### Summary: Why 17 Digits?
Standard double-precision floats are reliable up to about 15-16 decimal digits.
*   If you print `0.1 + 0.2` with **1 digit** of accuracy, the computer rounds `0.300...04` to `0.3`. It looks correct to the human eye.
*   The "Bonus Points" challenge in the video asked for **17 digits**. This forces the computer to display the "garbage digits" at the very end of the floating-point precision range, revealing the tiny `...0004` error that usually stays hidden.

The error is inherent to the fixed number of bits available in the **Mantissa**. You simply ran out of paper to write the number down.