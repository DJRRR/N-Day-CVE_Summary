## Group 1 Null Pointer Read

### Case-1 Code
```
void test(){
    char* s = NULL;
    printf("%s\n", s);
}
```

### Case-1 Res Clang & Gcc -fsanitize=addr
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

(1) 不过从unknown address 0x0可以知道是null pointer。
(2) 可以知道产生null pointer的source code line number。
(3) 无法直接知道指针name。
```

### Case-1 Res Gcc -fsanitize=null(Null pointer detect)
```
没有检测出Null pointer
Segmentation fault (core dumped)
```

### Case-1 Res Clang -fsanitize=null(Null pointer detect)
```
(null)
没报错。
```


### Case-2 Code
```
void test(){
    char* s = NULL;
    printf("%c\n", s[0]);
}
```

### Case-2 Gcc -fsanitize=null
```
case2.c:5:20: runtime error: load of null pointer of type 'char'
Segmentation fault (core dumped)
检测出是null pointer，并汇报错误行号。
```

### Case-2 Clang -fsanitize=null
```
==12249==ERROR: UndefinedBehaviorSanitizer: SEGV on unknown address 0x000000000000 (pc 0x00000042bcbc bp 0x7ffeb499a110 sp 0x7ffeb499a0f0 T12249)
==12249==The signal is caused by a READ memory access.
==12249==Hint: address points to the zero page.
    #0 0x42bcbb in test /home/djr/N-day/san-test/NullPointer/case2.c
    #1 0x42bcf3 in main /home/djr/N-day/san-test/NullPointer/case2.c:9:5
    #2 0x7fdce9d1982f in __libc_start_main /build/glibc-Cl5G7W/glibc-2.23/csu/../csu/libc-start.c:291
    #3 0x402ad8 in _start (/home/djr/N-day/san-test/NullPointer/case2_clang+0x402ad8)

```



---

## Group 2 Null Pointer Write

### Case-3 Code
```
void test(){
    char* s = NULL;
    s[0] = 'a';
}
```

### Case-3 Res Clang&Gcc -fsanitize=addr
```
==6534==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x00000040073e bp 0x7ffe337759a0 sp 0x7ffe33775990 T0)
    #0 0x40073d in test /home/djr/N-day/san-test/NullPointer/case2.c:5
    #1 0x400751 in main /home/djr/N-day/san-test/NullPointer/case2.c:9
    #2 0x7f30fbe1182f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
    #3 0x400628 in _start (/home/djr/N-day/san-test/NullPointer/case2+0x400628)

AddressSanitizer can not provide additional info.
SUMMARY: AddressSanitizer: SEGV /home/djr/N-day/san-test/NullPointer/case2.c:5 test

分析同Case-1 Res Clang&Gcc -fsanitize=addr
```

### Case-3 Res Clang -fsanitize=null
```
UndefinedBehaviorSanitizer:DEADLYSIGNAL
==12131==ERROR: UndefinedBehaviorSanitizer: SEGV on unknown address 0x000000000000 (pc 0x00000042bc7c bp 0x7ffdefb8a1d0 sp 0x7ffdefb8a1c0 T12131)
==12131==The signal is caused by a WRITE memory access.
==12131==Hint: address points to the zero page.
    #0 0x42bc7b in test /home/djr/N-day/san-test/NullPointer/case2.c
    #1 0x42bca3 in main /home/djr/N-day/san-test/NullPointer/case2.c:9:5
    #2 0x7f430e0d282f in __libc_start_main /build/glibc-Cl5G7W/glibc-2.23/csu/../csu/libc-start.c:291
    #3 0x402a98 in _start (/home/djr/N-day/san-test/NullPointer/case2_clang+0x402a98)

```


### Case-3 Res Gcc -fsanitize=null
```
case2.c:5:10: runtime error: store to null pointer of type 'char'
Segmentation fault (core dumped)
检测出是null pointer，并汇报错误行号。

```