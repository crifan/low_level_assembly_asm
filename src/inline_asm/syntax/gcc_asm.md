# GCC中关于asm的语法

* GCC中关于asm的语法
  * `标准C`语言（用`-ansi`或`-std`去编译）：用`__asm__`
  * `GNU扩展`（`GNU extension`）：用`asm`

背景：

当用`标准C`（`-ansi`或`-std`），会禁用掉部分关键字：

* `asm`
* `typeof`
* `inline`
  * 特例：当`-std=c99`或更新版本，可以用`inline`
* `restrict`
  * 只有当`-std=gnu99`或`-std=c99`（等价的`-std=iso9899:1999`）的或更新版 时，才能用`restrict`

-》想要用上述关键字的话，解决办法是：

在前面和后面分别加上2个下划线

-》对于asm，可以自己加上对应定义：

```c
#ifndef __GNUC__
#define __asm__ asm
#endif
```
