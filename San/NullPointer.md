## Group 1 Null Pointer Read

### Case-1 Code
```
void test(){
    char* s = NULL;
    printf("%s\n", s);
}
```

### Case-1 Res
```
==6348==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x7f6122661746 bp 0x7ffff478f360 sp 0x7ffff478f328 T0)
    #0 0x7f6122661745 in strlen (/lib/x86_64-linux-gnu/libc.so.6+0x8b745)
    #1 0x7f612264569b in _IO_puts (/lib/x86_64-linux-gnu/libc.so.6+0x6f69b)
    #2 0x400701 in test /home/djr/N-day/san-test/NullPointer/case1.c:5
    #3 0x400712 in main /home/djr/N-day/san-test/NullPointer/case1.c:9
    #4 0x7f61225f682f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
    #5 0x400618 in _start (/home/djr/N-day/san-test/NullPointer/case1+0x400618)

AddressSanitizer can not provide additional info.
SUMMARY: AddressSanitizer: SEGV ??:0 strlen

(1) Address Sanitizer没有Null Pointer这种Vuln Type。
(2) 不过从unknown address 0x0可以知道是null pointer。
(3) 可以知道产生null pointer的source code line number。
(4) 无法直接知道指针name。
```

---

## Group 2 Null Pointer Write

### Case-2 Code
```
void test(){
    char* s = NULL;
    s[0] = 'a';
}
```

### Case-2 Res
```
==6534==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x00000040073e bp 0x7ffe337759a0 sp 0x7ffe33775990 T0)
    #0 0x40073d in test /home/djr/N-day/san-test/NullPointer/case2.c:5
    #1 0x400751 in main /home/djr/N-day/san-test/NullPointer/case2.c:9
    #2 0x7f30fbe1182f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
    #3 0x400628 in _start (/home/djr/N-day/san-test/NullPointer/case2+0x400628)

AddressSanitizer can not provide additional info.
SUMMARY: AddressSanitizer: SEGV /home/djr/N-day/san-test/NullPointer/case2.c:5 test

分析同上
```