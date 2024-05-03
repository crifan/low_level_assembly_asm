# 常量

* 汇编中的常量值
  * ARM syntax：`#xxx`
    * 举例
      * 10进制
        * `mov x16, #338`
        * `ADD R7, R7, #4 ; a = a + 4`
        * `sub  r0, r1, #3  @ r0 = r1 - 3`
      * 16进制：0x开头
        * `svc #0x80`
        * `SUB R8, R7, #0xC ; b = a − 12`
        * `mov  r0, #0xFF0  @ r0 = 0xFF0`
  * Intel syntax：h结尾：`xxxh`
    * 举例：
      * `int   80h`
  * AT&T的：`$xxx`
    * 举例
      * `int   $0x80`
