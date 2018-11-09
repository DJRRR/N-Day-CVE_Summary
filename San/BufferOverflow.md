# Address Sanitizer

---
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
 This frame has 2 object(s):
 [32, 432) 'buf' <== Memory access at offset 452 overflows this variable
 [480, 880) 'buf2'
 
 Address Sanitizer判断正确

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

Address Sanitizer判断错误
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
#0 0x7f081c3e2602 in malloc (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x98602)
#1 0x400743 in test /home/djr/N-day/san/test.c:10
#2 0x4007b7 in main /home/djr/N-day/san/test.c:18
#3 0x7f081bfa082f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)

SUMMARY: AddressSanitizer: heap-buffer-overflow /home/djr/N-day/san/test.c:12 test

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

这里把offset 432认为是buf2的unferflow。
```


