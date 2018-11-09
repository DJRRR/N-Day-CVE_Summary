## Group 1 Format String

### Case-1 Code
```
void test(){
    char s[8];
    scanf("%s",s);
    printf(s);
}
```

### Case-1 Res
```
编译的时候会报warning，假如没有Ignore warning的话。
case1.c:6:12: warning: format not a string literal and no format arguments [-Wformat-security]
printf(s);

payload 1 : %n --> address sanitizer无报错。

payload 2 : %10$n ---> add
==6645==ERROR: AddressSanitizer: SEGV on unknown address 0x006e24303125 (pc 0x7fac52d66bc6 bp 0x7ffd165eff80 sp 0x7ffd165ef950 T0)
    #0 0x7fac52d66bc5  (/lib/x86_64-linux-gnu/libc.so.6+0x4cbc5)
    #1 0x7fac52d674a5 in vfprintf (/lib/x86_64-linux-gnu/libc.so.6+0x4d4a5)
    #2 0x7fac531447d0 in __interceptor_vprintf (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x607d0)
    #3 0x7fac53144907 in __interceptor_printf (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x60907)
    #4 0x4009b3 in test /home/djr/N-day/san-test/FormatString/case1.c:6
    #5 0x400a27 in main /home/djr/N-day/san-test/FormatString/case1.c:11
    #6 0x7fac52d3a82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
    #7 0x400838 in _start (/home/djr/N-day/san-test/FormatString/case1+0x400838)

AddressSanitizer can not provide additional info.
SUMMARY: AddressSanitizer: SEGV ??:0 ??

这是因为format string造成的错误地址写，所以仅从address sanitizer的report中无法判断format string漏洞，
但是从编译的warning也许能判断。

```