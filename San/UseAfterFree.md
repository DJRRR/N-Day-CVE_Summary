## Group 1 Use After Free

### Case-1 Code
```
void test(){
    int *a = (int *)malloc(sizeof(int) * 100);
    a[0] = 1;
    free(a);
    printf("%d\n",a[0]);
}
```

### Case-1 Res
```
READ of size 4 at 0x61400000fe40 thread T0
    #0 0x40096f in test /home/djr/N-day/san-test/UseAfterFree/case1.c:7 -> Vuln. Point
    #1 0x400997 in main /home/djr/N-day/san-test/UseAfterFree/case1.c:11
    #2 0x7f12be36a82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)
    #3 0x400808 in _start (/home/djr/N-day/san-test/UseAfterFree/case1+0x400808)

0x61400000fe40 is located 0 bytes inside of 400-byte region [0x61400000fe40,0x61400000ffd0)
freed by thread T0 here:
    #0 0x7f12be7ac2ca in __interceptor_free (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x982ca)
    #1 0x400938 in test /home/djr/N-day/san-test/UseAfterFree/case1.c:6 -> Free
    #2 0x400997 in main /home/djr/N-day/san-test/UseAfterFree/case1.c:11
    #3 0x7f12be36a82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)

previously allocated by thread T0 here:
    #0 0x7f12be7ac602 in malloc (/usr/lib/x86_64-linux-gnu/libasan.so.2+0x98602)
    #1 0x4008e7 in test /home/djr/N-day/san-test/UseAfterFree/case1.c:4 -> Malloc
    #2 0x400997 in main /home/djr/N-day/san-test/UseAfterFree/case1.c:11
    #3 0x7f12be36a82f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)

SUMMARY: AddressSanitizer: heap-use-after-free /home/djr/N-day/san-test/UseAfterFree/case1.c:7 test

(1)Address Sanitizer可以识别heap-use-after-free。
(2)可以给出malloc, free, 以及触发use-after-free的source code line number。

```