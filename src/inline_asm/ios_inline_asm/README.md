# iOS中ARM内嵌汇编

## iOS中内嵌汇编的语法

iOS中内嵌汇编，其实是：XCode（内部的）clang编译器中，内嵌asm汇编

此处目标设备是iPhone7，内部ARM架构

所以汇编是针对于64位的ARM的arm64，调用规范是：

* AAPCS64=Procedure Call Standard for the Arm 64-bit Architecture

具体的asm的语法，也基本上兼容：

* Extended Asm
  * == 上面已总结的：`extend asm`

具体语法详见：

* [GCC-Inline-Assembly-HOWTO (ibiblio.org)](https://www.ibiblio.org/gferg/ldp/GCC-Inline-Assembly-HOWTO.html#s6)
* [How to Use Inline Assembly Language in C Code — gcc 6 documentation (dmalcolm.fedorapeople.org)](https://dmalcolm.fedorapeople.org/gcc/2015-08-31/rst-experiment/how-to-use-inline-assembly-language-in-c-code.html)

## 举例

### iOS内嵌汇编

```c
__attribute__((always_inline)) long svc_0x80_syscall(int syscall_number, const char * pathname, struct stat * stat_info) {
    long ret = 0;
    long long_syscall_number = syscall_number;
    __asm__ volatile(
         "mov x0, %[pathname_p]\n"
         "mov x1, %[stat_info_p]\n"
         "mov x16, %[long_syscall_number_p]\n"
         "svc #0x80\n"
         "mov %[ret_p], x0\n"
        : [ret_p]"=r"(ret)
        : [long_syscall_number_p]"r"(long_syscall_number), [pathname_p]"r"(pathname), [stat_info_p]"r"(stat_info)
    );
    return ret == 0 ? ret : -1;
}
...
openResult = svc_0x80_syscall(SYS_stat64, filePathStr, &stat_info);
```

#### 优化后：用寄存器register保存参数变量值

此次之前代码：

```c
__attribute__((always_inline)) long svc_0x80_syscall(int syscall_number, const char * pathname, struct stat * stat_info) {
        long ret = 0;
        long long_syscall_number = syscall_number;
        __asm__ volatile(
             "mov x0, %[pathname_p]\n"
             "mov x1, %[stat_info_p]\n"
             "mov x16, %[long_syscall_number_p]\n"
             "svc #0x80\n"
             "mov %[ret_p], x0\n"
            : [ret_p]"=r"(ret)
            : [long_syscall_number_p]"r"(long_syscall_number), [pathname_p]"r"(pathname), [stat_info_p]"r"(stat_info)
             : "x0", "x1", "x16"
        );
        return ret == 0 ? ret : -1;
}
```

继续优化：用寄存器register保存参数变量值

最后代码是：

```c
__attribute__((always_inline)) int svc_0x80_syscall(int syscall_number, const char * pathname, struct stat * stat_info) {
    register const char * x0_pathname asm ("x0") = pathname; // first arg
    register struct stat * x1_stat_info asm ("x1") = stat_info;  // second arg
    register int x16_syscall_number asm ("x16") = syscall_number; // special syscall number store to x16

    register int x4_ret asm("x4") = -1; // store result

    __asm__ volatile(
         "svc #0x80\n"
         "mov x4, x0\n"
        : "=r"(x4_ret)
        : "r"(x0_pathname), "r"(x1_stat_info), "r"(x16_syscall_number)
//         : "x0", "x1", "x4", "x16"
    );
    return x4_ret;
}
```

C代码调用：

```c
#import <sys/syscall.h>

struct stat stat_info;
const char * filePathStr = [filePath UTF8String];
...
openResult = svc_stat64(SYS_stat64, filePathStr, &stat_info);
```

说明：

虽然没有彻底搞懂：

inline asm的extend asm的

* 语法
* 逻辑
* 注意事项

但是至少是能跑通实现：

* svc 0x80的（stat和）stat64的调用
* 返回期望的值

