---
title: 'CSAPP第二章学习记录'
publishDate: 2025-3-1
description: 'CSAPP第二章学习记录.'
tags:
  - CSAPP
language: 'Chinese'
heroImage: { src: './thumbnail.jpg', color: '#D58388' }
---

## lecture2

#### **Two's Complement**

位宽：设二进制位数为 𝑛

无符号数范围：
$$
0\leq U \leq 2^n-1
$$
补码范围：
$$
-2^{n-1}≤𝑇≤2^{n-1}-1
$$
#### **无符号数与补码转换**

- 无符号数转补码

$$
T =
\begin{cases}
U, & \text{if } U < 2^{n-1} \\
U - 2^n, & \text{if } U \geq 2^{n-1}
\end{cases}
$$

- 补码转无符号数
  $$
  U =
  \begin{cases}
  T, & \text{if } T \geq 0 \\
  T + 2^n, & \text{if } T < 0
  \end{cases}
  $$

####  拓展一个数的位表示

1. 考虑 -12345的补码表示，16位

   ```
   原码：1011 0000 0011 1001  #最高位表示负数
   反码：1100 1111 1100 0110
   补码：1100 1111 1100 0111  # 反码加1
   十六进制：0xCFC7
   ```

2. 53191的无符号表示（16位无符号整数）：

   ```
   二进制：1100 1111 1100 0111  # 直接转换十进制
   十六进制：0xCFC7
   ```

   

3. 考虑 -12345的补码表示，32位

   ```
   原码：11111111 11111111 10110000 00111001
   反码：10000000 00000000 01001111 11000110
   补码：10000000 00000000 01001111 11000111  # 反码加1
   十六进制：0xFFFFCFC7
   ```

4. 53191的无符号表示（322位无符号整数）：

   ```
   二进制：00000000 00000000 11001111 11000111  # 高位补零
   十六进制：0x0000CFC7

#### 为什么会产生补码这个奇怪的玩意
1. 统一加减法：
   1. **加法**：若两数符号相同，直接相加；若符号不同，需比较绝对值大小，再进行减法。
   2. **减法**：需转换为加法，但需处理借位和符号判断。
   3. **硬件复杂度**：需要额外的电路处理符号位和运算逻辑。
   
#### 逻辑和算术右移
1. 逻辑右移：无符号数右移，高位补0，用于纯数值操作。
2. 算术右移：有符号数右移，高位补符号位，用于保持数学意义的除法
#### 逻辑和算术左移


#### 补码的本质与溢出机制
1. 溢出行为：当计算结果超出补码的表示范围时，高位会被截断，仅保留低 n 位，并仍按补码规则解释。
   `65535×65535=4,294,836,225`发生了溢出，32位截断的结果是0xfffe0001，补码解释是-131071。

#### 最小负数的特性
1. 最小负数是`-2^{n-1}`,补码是`100....0`，取-x的情况是`2^{n-1}`，已经发生了溢出，例如`x=-128`，取反。补码是`1000000`，`~x+1`的结果是`10000000`,任然是-128。

#### IEEE规格浮点数表示
1. **符号位（Sign Bit）**：1位（第31位）
   - 0表示正数，1表示负数。
2. **指数（Exponent）**：8位（第23位到第30位）
   - 用来表示数字的规模（大小范围），采用“偏置”表示法。
3. **尾数（Mantissa，或称Fraction）**：23位（第0位到第22位）
   - 表示数字的有效数字部分（小数部分）。

整个浮点数的值可以用公式表示为：

$$
值 = (-1)^{\text{符号位}} \times 2^{\text{指数 - 偏置}} \times (1 + \text{尾数})
$$
![IEEE 754单精度结构](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d2/Float_example.svg/1200px-Float_example.svg.png)

**IEEE 754还定义了一些特殊值：**

1. **零**：指数和尾数全为0（符号位可以是0或1，表示+0或-0）。
   - 例如：0 00000000 00000000000000000000000 是 +0。
2. **无穷大（Infinity）**：指数全1，尾数全0。
   - 例如：0 11111111 00000000000000000000000 是正无穷。
3. **NaN（Not a Number）**：指数全1，尾数非全0。
   - 例如：0 11111111 10000000000000000000000 是NaN，表示无效运算结果（如0/0）。

#### 非规格

**指数全是0，*
$$
值 = (-1)^{\text{符号位}} \times 2^{\text{1 - 偏置}} \times ( \text{尾数})
$$

#### **单精度浮点数转换示例**

**问题**：将十进制数 **-13.625** 转换为IEEE 754单精度二进制。

1. 负数 → **符号位 = 1**
2. **整数部分**：13 → `1101`
3. **小数部分**：0.625 → `0.101`（因为0.625 = 1×2⁻¹ + 0×2⁻² + 1×2⁻³）
4. **合并结果**：13.625 = `1101.101`
5. 二进制科学计数法：`1101.101` = `1.101101 × 2³`
6. **尾数**：`101101`（去掉前导1，补零到23位 → `10110100000000000000000`）
7. **指数**：实际指数3 → 存储指数 = 3 + 127 = 130 → 二进制 `10000010`
8. **完整二进制** `1 10000010 10110100000000000000000`

#### 向偶数舍入（Round to nearest, ties to even）

1. **规则**：优先舍入到最接近的值；若处于两个值的正中间，则选择最低有效位为偶数的结果。
   - 示例
     - 十进制：`2.5 → 2`（偶），`3.5 → 4`（偶）。
     - 二进制：`10.10100 → 10.10`（末位0为偶）









