## Group 1 Integer Overflow

### Case-1 Code
```
void test(){
    unsigned int i = 0xffffffff;
    unsigned int j = i + 1;
    printf("%u\n", j);
}
```

### Case-1 Res
```
0
(1) Address Sanitizer无法检测出unsigned int的overflow。
(2) 编译时用gcc -g -fsanitizer=address连warning都不会给。
```

### Case-2 Code
```
void test(){
    int i = 0x7fffffff;
    int j = i + 1;
    printf("%d\n",j);
}
```

### Case-2 Res
```
-2147483648
同上无warning无error。
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

### Case-3 Res
```
-1
同上无报错无warning。
```