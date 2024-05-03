# 内联汇编相关关键字

* asm（汇编）关键字
  * GNU/GCC编译器
    * 标准C语言（用-ansi或-std去编译）：用`__asm__`
    * GNU扩展（GNU extension）：用`asm`
  * ARM编译器、微软的内嵌汇编
    * `__asm`
* volatile关键字
  * 属于asm的修饰符qualifier
    * GNU/GCC编译器
      * 标准C语言（用`-ansi`或`-std`去编译）：用`__volatile__`
      * GNU扩展（`GNU extension`）：用`volatile`

-> 结论：

从兼容性来说，那最好用：

* 前后都带下划线的版本：
  * `__asm__`
  * `__volatile__`
