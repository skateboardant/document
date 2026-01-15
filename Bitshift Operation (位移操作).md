# Bitshift Operation (位移操作)

## 1. D Latch

<img src="C:\Users\skateboardant\AppData\Roaming\Typora\typora-user-images\image-20260115142711026.png" alt="image-20260115142711026" style="zoom:43%;" />

​	A latch is designed to hold a single bit information, reflected on its output Q.

​	A D-Latch has two inputs, a data input and an enable input. When the enable signal is off, the value passed to the data input doesn't matter, the stored value won't change. The only way to rewrite the latch's content is to set the enable signal to 1 while applying the desired input value in the data input. So, a latch can be seen as a memory cell capable of storing one bit of information. 

## 2. D Flip-flop

<img src="C:\Users\skateboardant\AppData\Roaming\Typora\typora-user-images\image-20260115144426912.png" alt="image-20260115144426912" style="zoom:43%;" />

​	Flip-flop, internally is built using a latch. The key difference is that flip-flop includes a special component called an **edge detector**, connected to the enable signal of the internal latch. 

​	An edge detector takes an input signal like a clock, and produces a very short pulse whenever that <u>signal changes from 0 to 1 or 1 to 0</u>, depending on the type of the edge detector. 

​	A flip-flop might receive a clock signal that stays high for a considerable of time. However, since the enable signal of the internal latch is driven by the edge detector's output, the actual window for writing data is reduced to <u>just the instant of the rising edge</u>. As a result the flip-flop only caputures the value present as its data input at the precious moment the signal transitions from 0 to 1. After that, it doesn't matter if the clock stays high. Any value on data input will be ignored. The edge has already occured, so the flip-flop won't overwrite its value until the next rising edge. 



<u>In short, while latches are level triggered, flip-flops are edge triggered.</u> 



​	Connect several flip-flops so that the Q output of one, become the D input of the next, while they all share the same signal. 

​	The only way to load data into the system is through the D input of the first flip-flop. After that, each clock tick causes the value to shift to the next flip-flop in a chain. 

​	A **shift register** is a type of digital circuit built using a <u>cascade of flip-flops</u>, where the output of one flip-flop is connected to the input of the next. They all share a single clock signal which causes the data stored in the system to shift from one lofcation to the next with each clock pulse. 



## 3. Bidirectional Shift Register

<img src="C:\Users\skateboardant\AppData\Roaming\Typora\typora-user-images\image-20260114214937619.png" alt="image-20260114214937619" style="zoom:43%;" />

​	Bidirectional shift register allows each flip-flop to overwrite its content with the value stored in the flip-flop on the right or on the left. A bidirectional shift register can toggle its shifting direction, ~~so we can move all bits to the right or to the left when we need to.~~ \



## 4. Parallel-in, Parallel-out Bidirectional Shift Register

<img src="C:\Users\skateboardant\AppData\Roaming\Typora\typora-user-images\image-20260114215635964.png" alt="image-20260114215635964" style="zoom:43%;" />

​	Parallel-in, parallel-out bidirectionals shift register allow parallel read and write operations. While one input toggles the shift direction, another switches between shift mode and write mode. 



## 5.  << (Left Shift)                        >> (Right Shift)

​	Shift operation requires two operand. Take the first operand (variable) and shift its bits either to the left or right. The second operand specifies how many positions the bits inside the variable should move. 

​	When binary data is shifted in any direction, an empty bit appears on one end, and a bit is pushed out on the other. 

​	Parallel-in, parallel-out register still needs a serial input and output. If we wanna shift data in any direction, the serial input tells the circuit how to fill the register during shift. The serial output is also important, because the bit being pushed out might be needed elsewhere. Instead of filling with ones or zeros, we can feedback the bit that was just pushed out, creating what's known as a circuit or round shoift operation. 

​	In binary, shifting bits to the left and filling with zeros, is equivalent to multiplying by 2, shifting bits to the right and filling with zeros, is equivalent to deviding by 2.

e.g.
$$ 0011 (3) << 1 = 0110 (6) $$ 

$$1110 (14) >>  1 = 0111 (7) $$

$$ 1000 (8) >> 2 = 0010 (2) $$



## Why <<,  >> is faster than $\times$ 2,  $\div$ 2 at low-level view?

​	A single instruction doesn't necessarily mean a single clocck cycle. When we use CPU's multiplication $\times$ or division $\div$ instruction, the number of cycles depends on the operands, and it can takes dozens of cycles. By contrast, a bit shift operation, regardless of the operand, always just takes one clock cycle. 



Note: 

- This method works only when multipling or deviding by powers of 2. 

- Morden compilers are very good at detecting these situations when the optimization.

- Pictures above source: Core Dumped :smile: