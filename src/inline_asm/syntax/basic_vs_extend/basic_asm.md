# basic asm = 基本asm

## 语法

```c
asm("assembly code");
```

或：

```bash
__asm [volatile] (code); /* Basic inline assembly syntax */
```

## 举例

### 单行

```c
asm("movl %ecx %eax"); /* moves the contents of ecx to eax */
__asm__("movb %bh (%eax)"); /*moves the byte from bh to the memory pointed by eax */
```

### 多行

最后加上：`\n\t`

```c
__asm__ ("movl %eax, %ebx\n\t"
          "movl $56, %esi\n\t"
          "movl %ecx, $label(%edx,%ebx,$4)\n\t"
          "movb %ah, (%ebx)");
```

解释：编译器`gcc`会把汇编代码发送到汇编器：`as`=`GAS`，其中多行的分隔符就是：`\n\t`

### 微软的内嵌汇编

```c
__asm int 3
```

```c
__asm {
   mov al, 2
   mov dx, 0xD007
   out dx, al
}
```

```c
__asm mov al, 2
__asm mov dx, 0xD007
__asm out dx, al
```

```c
__asm mov al, 2   __asm mov dx, 0xD007   __asm out dx, al
```

```c
// asm_overview.cpp
// processor: x86
void __declspec(naked) main()
{
    // Naked functions must provide their own prolog...
    __asm {
        push ebp
        mov ebp, esp
        sub esp, __LOCAL_SIZE
    }


    // ... and epilog
    __asm {
        pop ebp
        ret
    }
}
```
