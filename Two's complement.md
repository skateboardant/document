# Two's complement

## Introduction to two's complement

​	When we simply <u>invert all the bits in a binary number</u>, it is called **one's complement** of that number. <u>Adding one to the one's complement</u> gives us the **two's complement**. 

​	In two's complement system, negative of a binary number, is stored as its two's complement. Positive numbers are represented as themselves. 

​	So, the correct method to represent a negative number in two's system is:

1. First of all, we translate absolute value of the number to binary. 
2. Then, we flip every bit of the binary representation. 
3. And, we add one to the flipped number to get the final result. 



​	In this system, the most significant bit of all the negative numbers would be 1, and the most significant bit of all positive numbers would be 0. So, we can call the most most significant bit as sign bit, because by looking at this bit, we can decide whether it is a positive number or negative number. 

​	In this encoding mechanism, there is only one notation of 0, and we can store value from $-2^{n-1} \text{ to } 2^{n-1}-1$ 



## Why we use two's complement?

​	One of advantages is that, two's complement can simplify hardware implementation of arithmetic functions. 

​	In two's complement, for addition and subtraction, the hardware can ignore the sign of operand. For multiplication, hardware can also ignore the sign of operand if the product is required to keep the same number of bits as operands. Note the this property does not hold true for division. 

​	So, in this way, there is no need for hardware to care about the sign bit, which makes Arithmetic Logic Unit (ALU) calculate correctly for both unsigned number and signed number. 