## Group 1 Integer Overflow

### Case-1 Code
```
void test(){
    unsigned int i = 0xffffffff;
    unsigned int j = i + 1;
    printf("%u\n", j);
}
```

### Case-1 Gcc Res
```
gcc的integer sanitizer只提供integer-divide-by-zero，signed-integer-overflow
输出0
无warning无报错
```

### Case-1 Clang Res
```
clang -g -o case_clang case1.c -fsanitize=integer
SANITIZER_GROUP("integer", Integer,
                    ImplicitConversion | IntegerDivideByZero | Shift |
                    SignedIntegerOverflow | UnsignedIntegerOverflow)
case1.c:5:23: runtime error: unsigned integer overflow: 4294967295 + 1 cannot be represented in type 'unsigned int'
0
检测出unisgned integer overflow，并输出错误行号。

```

### Case-2 Code
```
void test(){
    int i = 0x7fffffff;
    int j = i + 1;
    printf("%d\n",j);
}
```

### Case-2 Res Gcc
```
gcc -g -o case2_gcc case2.c -fsanitize=signed-integer-overflow
case2.c:5:9: runtime error: signed integer overflow: 2147483647 + 1 cannot be represented in type 'int'
-2147483648
检测出signed integer overflow，并输出错误行号。

```

---

## Group 2 Type Cast

### Case-3 Code
```
void test(){
    unsigned int i = 0xffffffff;
    int j = (int)i;
    printf("%d\n",j);
}
```

### Case-3 Res Gcc & Clang
```
输出-1
无报错无warning。
```