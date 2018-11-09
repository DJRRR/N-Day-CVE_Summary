## Group 1 Stack Overflow

* Shadow bytes的长度有限，所以可能存在buf判断错误的情况。
在开了编译-g的情况下，会给出buf的变量名以及出错的源代码行号。

### Case-1 Code 
```aidl
void test(){
    int buf[100];
    int buf2[100];
    buf[105]=9;

}
```

### Case-1 Res
```aidl
#0 0x400942 in test /home/djr/N-day/san-test/BufOverflow/case1.c:6
#1 0x4009b7 in main /home/djr/N-day/san-test/BufOverflow/case1.c:11
#2 0x7fa3ec26b82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
#3 0x400798 in _start (/home/djr/N-day/san-test/BufOverflow/case1+0x400798)

 This frame has 2 object(s):
 [32, 432) 'buf' <== Memory access at offset 452 overflows this variable
 [480, 880) 'buf2'
SUMMARY: AddressSanitizer: stack-buffer-overflow /home/djr/N-day/san-test/BufOverflow/case1.c:6 test

 (1) 可以检测出Stack Overflow。
 (2) 可以给出造成overflow的source code line number。
 (3) 可以正确判断造成overflow的stack variable的name。

```

### Case-2 Code

```aidl
void test(){
    int buf[100];
    int buf2[100];
    buf[106] = 9;
}
```

### Case-2 Res
```aidl
This frame has 2 object(s):
[32, 432) 'buf'
[480, 880) 'buf2' <== Memory access at offset 456 underflows this variable

 #0 0x4008e1 in test /home/djr/N-day/san-test/BufOverflow/case2.c:6
 #1 0x4009bd in main /home/djr/N-day/san-test/BufOverflow/case2.c:10
 #2 0x7ff7e164b82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
 #3 0x400748 in _start (/home/djr/N-day/san-test/BufOverflow/case2+0x400748)
SUMMARY: AddressSanitizer: stack-buffer-overflow /home/djr/N-day/san-test/BufOverflow/case2.c:6 test

(1) 可以检测出Stack Overflow。
(2) 可以给出造成overflow的source code line number。
(3) 无法正确判断造成overflow的stack variable。（因为shadow bytes长度有限）
```
---

## Group 2 Heap Overflow
不同于Stack Overflow，可以给出出错的行号，但不能给出指向heap的那个变量的name。

### Case-3 Code
```aidl
int *buf;
int *buf2;
void test(){
    buf = (int *)malloc(sizeof(int) * 100);
    buf2 = (int *)malloc(sizeof(int) * 100);
    buf[101] = 9;
}
```

### Case-3 Res
```aidl
Overflow Location:
#0 0x4007a0 in test /home/djr/N-day/san-test/BufOverflow/case3.c:9
#1 0x4007b7 in main /home/djr/N-day/san-test/BufOverflow/case3.c:13
#2 0x7fbb7d98f82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
#3 0x400668 in _start (/home/djr/N-day/san-test/BufOverflow/case3+0x400668)

Define Location:
#0 0x7fd28b1ef602 in malloc (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x98602)
#1 0x400743 in test /home/djr/N-day/san-test/BufOverflow/case3.c:7
#2 0x4007b7 in main /home/djr/N-day/san-test/BufOverflow/case3.c:13
#3 0x7fd28adad82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
SUMMARY: AddressSanitizer: heap-buffer-overflow /home/djr/N-day/san-test/BufOverflow/case3.c:9 test
(1) 可以检测出是Heap Overflow
(2) 可以给出造成overflow的source code line number，同时给出malloc的Line。
(3) 无法给出指向heap chunk的变量名。

```

---

## Group 3 Memory Copy
会产生奇怪的错误。

### Case-4 Code
```aidl
int *toCopy;
void test(){
    int buf[100];
    int buf2[200];
    toCopy = (int *)malloc(sizeof(int) * 400);
    memcpy(buf, toCopy, sizeof(int) * 105);
}

```

### Case-4 Res
```aidl
This frame has 2 object(s):
[32, 432) 'buf'
[480, 1280) 'buf2' <== Memory access at offset 432 partially underflows this variable

 #0 0x7fa317d58903 in __asan_memcpy (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x8c903)
 #1 0x40097c in test /home/djr/N-day/san-test/BufOverflow/case4.c:9
 #2 0x4009f1 in main /home/djr/N-day/san-test/BufOverflow/case4.c:13
 #3 0x7fa31792282f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
 #4 0x4007d8 in _start (/home/djr/N-day/san-test/BufOverflow/case4+0x4007d8)
SUMMARY: AddressSanitizer: stack-buffer-overflow ??:0 __asan_memcpy

(1)可以检测出是Stack Overflow。
(2)可以正确判断出错的source code line number。
(3)在判断是哪个变量overflow的时候出现了奇怪的错误，把buf的overflow认为是buf2的underflow。
```


