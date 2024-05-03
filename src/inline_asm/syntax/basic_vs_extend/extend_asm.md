# extend asm = 扩展asm

## 语法

Extended inline assembly syntax：

```c
asm [asm-qualifiers] ( AssemblerTemplate 
                      : OutputOperandsList
                      [: InputOperandsList       /* optional */
                      [: ClobbersRegistersList   /* optional */
                      [: GotoLabels] ]            /* optional */
                    ])
```

* 说明
  * asm-qualifiers可选值：`volatile`

## 举例

### 拷贝src到dst，且给dst加1

```c
int src = 1;
int dst;   

asm ("mov %1, %0\n\t"
    "add $1, %0"
    : "=r" (dst) 
    : "r" (src));

printf("%d\n", dst);
```

### fills the fill_value count times to the location pointed to by the register edi

* fills the fill_value count times to the location pointed to by the register edi
  * 同时告诉gcc：
    * 受影响的寄存器有：eax、edi
      * eax、edi寄存器中的内存已无效

```c
asm ("cld\n\t"
      "rep\n\t"
      "stosl"
      : /* no output registers */
      : "c" (count), "a" (fill_value), "D" (dest)
      : "%ecx", "%edi" 
      );
```

### make the value of ’b’ equal to that of ’a’

```c
int a=10, b;
asm ("movl %1, %%eax; 
      movl %%eax, %0;"
      :"=r"(b)        /* output */
      :"r"(a)         /* input */
      :"%eax"         /* clobbered register */
      );
```

参数和含义解释：

* "b" is the output operand, referred to by %0 and "a" is the input operand, referred to by %1.
* "r" is a constraint on the operands. We’ll see constraints in detail later. For the time being, "r" says to GCC to use any register for storing the operands. output operand constraint should have a constraint modifier "=". And this modifier says that it is the output operand and is write-only.
  * 关于操作数的限制constraint，详见
    * [6.1 Commonly used constraints- GCC-Inline-Assembly-HOWTO (ibiblio.org)](https://www.ibiblio.org/gferg/ldp/GCC-Inline-Assembly-HOWTO.html#s6)
* There are two %’s prefixed to the register name. This helps GCC to distinguish between the operands and registers. operands have a single % as prefix.
* The clobbered register %eax after the third colon tells GCC that the value of %eax is to be modified inside "asm", so GCC won’t use this register to store any other value.
