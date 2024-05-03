# asm基本语法

* asm asm-qualifiers ( AssemblerInstructions )
  * asm
    * 当用`-ansi`或`-std`时，要写成：__asm__
  * asm-qualifiers
    * `volatile`
      * 其实没啥作用
      * 所有的asm本身就是隐式声明为volatile的
    * `inline`
      * asm汇编代码（块）会尽量少的占用空间
  * AssemblerInstructions
    * 汇编代码
    * 会被assembler汇编器去汇编
    * 可以包含多行汇编代码
    * 多行之间加上换行分隔符
      * 常见的有：`\n\t`
      * 偶尔的有：`分号`=`semicolons`
        * 注意：个别汇编器中分号放在行首意思是注释
