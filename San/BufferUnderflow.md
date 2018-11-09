## Group 1 Stack Underflow

### Case-1 Code
```
void test(){
    int buf[100];
    int buf2[100];
    printf("%d\n", buf2[-1]);
}
```

### Case-1 Res
```
This frame has 2 object(s):
[32, 432) 'buf'
[480, 880) 'buf2' <== Memory access at offset 476 underflows this variable

#0 0x4009e0 in test /home/djr/N-day/san-test/BufUnderflow/case1.c:6
#1 0x400ad1 in main /home/djr/N-day/san-test/BufUnderflow/case1.c:11
#2 0x7ff6a295a82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
#3 0x400848 in _start (/home/djr/N-day/san-test/BufUnderflow/case1+0x400848)
SUMMARY: AddressSanitizer: stack-buffer-underflow /home/djr/N-day/san-test/BufUnderflow/case1.c:6 test

(1) 可以正确判断出是stack underflow。
(2) 可以正确判断出错的行号。
(3) 可以正确给出造成underflow的stack variable name。
```

### Case-2 Code
```
void test(){
    int buf[100];
    int a;
    int buf2[100];
    printf("%d\n", buf[0]); ---->防止在编译的时候被eliminated掉。
    printf("%d\n",buf2[-8]);
}
```

### Case-2 Res
```
 This frame has 2 object(s):
 [32, 432) 'buf' <== Memory access at offset 448 overflows this variable
 [480, 880) 'buf2'

#0 0x4009e0 in test /home/djr/N-day/san-test/BufUnderflow/case2.c:7
#1 0x400ad1 in main /home/djr/N-day/san-test/BufUnderflow/case2.c:12
#2 0x7f1c092c282f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
#3 0x400848 in _start (/home/djr/N-day/san-test/BufUnderflow/case2+0x400848)
SUMMARY: AddressSanitizer: stack-buffer-overflow /home/djr/N-day/san-test/BufUnderflow/case2.c:8 test

(1)将stack underflow认为是stack overflow。
(2)可以正确判断出错的行号。
(3)给出了错误的stack variable name。(buf2误判成buf)
```

---

## Group 2 Heap Underflow

### Case-3 Code
```
int* buf;
int* buf2;
void test(){
    buf = (int*)malloc(sizeof(int) * 100);
    buf2 = (int*)malloc(sizeof(int) * 100);
    buf2[-1] = 9;
}
```

### Case-3 Res
```
Underflow Location:
 #0 0x40079d in test /home/djr/N-day/san-test/BufUnderflow/case3.c:9
 #1 0x4007b4 in main /home/djr/N-day/san-test/BufUnderflow/case3.c:13
 #2 0x7fd4f477482f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
 #3 0x400668 in _start (/home/djr/N-day/san-test/BufUnderflow/case3+0x400668)


Define Location:
 #0 0x7f4326c95602 in malloc (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x98602)
 #1 0x400754 in test /home/djr/N-day/san-test/BufUnderflow/case3.c:8
 #2 0x4007b4 in main /home/djr/N-day/san-test/BufUnderflow/case3.c:13
 #3 0x7f432685382f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)

SUMMARY: AddressSanitizer: heap-buffer-overflow /home/djr/N-day/san-test/BufUnderflow/case3.c:9 test

(1) 首先address sanitizer我试了一下好像没有heap buffer underflow的说法，都统一认为是overflow。
(2) 给出这个堆Malloc的source code line number，也可以给出这个堆overflow的source code line number。
(3) 无法给出指向heap chunk的variable name。


```


