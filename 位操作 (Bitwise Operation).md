# 位操作 (Bitwise Operation)



## Nmber representation

|Binary|Decimal|Hexadecimal|
|:---:|:---:|:---:|
|0|0|0|
|1|1|1|
|10|2|2|
|11|3|3|
|100|4|4|
|101|5|5|
|110|6|6|
|111|7|7|
|1000|8|8|
|1001|9|9|
|1010|10|A|
|1011|11|B|
|1100|12|C|
|1101|13|D|
|1110|14|E|
|1111|15|F|


### Binary:

​	只由0，1组成

​	composed of 0, 1.

| 2^5^ | 2^4^ | 2^3^ | 2^2^ | 2^1^ | 2^0^ |
| :--: | :--: | :--: | :--: | :--: | :--: |
|  32  |  16  |  8   |  4   |  2   |  1   |

1001~binary~  = $1 \times 8 + 0 \times 4 + 0 \times 2 + 1 \times 1 = 9$

### Decimal:

​	由0，1，2，3，4，5，6，7，8，9组成

​	composed of 0，1，2，3，4，5，6，7，8，9.

| 10^3^ | 10^2^ | 10^1^ | 10^0^ |
| :---: | :---: | :---: | :---: |
| 1000  |  100  |  10   |   1   |

789~decimal~ = 7 × 100 + 8 × 10 + 9 × 1

### Hexadecimal：

| 16^3^ | 16^2^ | 16^1^ | 16^0^ |
| :---: | :---: | :---: | :---: |
| 4096  |  256  |  16   |   1   |

​	由0，1，2，3，4，5，6，7，8，9，A, B, C, D, E, F组成

​	composed of 0，1，2，3，4，5，6，7，8，9，A, B, C, D, E, F.

0xA5~hex~  = 10 × 16 + 5 × 1 = 165



**Conversions**:

1. Binary to Decimal:

​		1101~binary~ = 1 × 8 + 1 × 4 + 0 × 2 + 1 × 1 = 13

2. Hexadecimal to Decimal:

​		0xE7~hex~ = 14 × 16 + 7 × 1 = 224

3. Decimal to Binary:

​		43~decimal~ = 101011

4. Hexadecimal to Binary:

​		为了方便，先将十六进制转到十进制，再转换为2进制

​		Tricks: In order to simplify, convert hexadecimal to decimal, and then convert decimal to binary

​		0xF7~hex~ = 11110111~binary~ 

5. Binary to Hexadecimal:

​		先将二进制转换为十进制，再转换为十六进制

​		First, convert binary to decimal, and then convert decimal to hexadecimal

​		01001101~binary~ = 0x4D~hex~

6. Decimal to Hexadecimal:

​		180~decimal~ = 0xB4~hex~



## Bitwise Operations

当操作数是二进制时，直接操作，当操作数是十进制 / 十六进制时，先转换成二进制，再进行操作

Manipulate directly when the input / operand is binary. If not, convert input / operand to bianry first and calculate.

1. AND    --    & 

|operand1|operand2|output|
|:---:|:---:|:---:|
|0|0|0|
|1|0|0|
|0|1|0|
|1|1|1|

清零某些位、判断奇偶（x & 1）

Usually used for clearing certain bit, checking odd or even numbers (x & 1).

e.g.

``` &
	0101010 (42)
&	1001001 (73)
	------------
	0001000 (8)
```



2. OR    --    |

|operand1|operand2|output|
|:---:|:---:|:---:|
|0|0|0|
|1|0|1|
|0|1|1|
|1|1|1|

将某些位置为 1（设定标志位）

Set certain positions to 1 (set the flag bits)

e.g.

``` |
	1010010 (82)
|	0101010 (42)
	-------------
	1111010 (122)
```



3. NOT    --    ~

|input|output|
|:---:|:---:|
|0|1|
|1|0|

 在补码系统中，~x 的结果等于 -(x + 1)

In the two's complement system, the result of ~x is equal to -(x + 1)

e.g.

``` ~
~	1010001 (81)
----------------
	0101110 (46)
```



4. XOR    --    ^
|operand1|operand2|output|
|:---:|:---:|:---:|
|0|0|0|
|1|0|1|
|0|1|1|
|1|1|0|

不使用临时变量交换两个数 (a = a^b; b = a^b; a = a^b;)

判断两个数是否相等（结果为0则相等）

简单的加密

Swapping two numbers without using a temporary variable (a = a^b; b = a^b; a = a^b;).
Checking if two numbers are equal (the result is 0 if they are equal).
Simple encryption.

e.g.

``` ^
	1010110 (86)
~	0111010 (58)
-----------------
	1101100 (108)
```

