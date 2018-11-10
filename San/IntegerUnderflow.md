## Group 1 Integer Underflow

### Case-1 Code
```
void test(){
    unsigned int i = 0;
    unsigned int j = i - 1;
    printf("%u\n",j);
}
```

### Case-1 Res Gcc
```
4294967295
无warning无报错。
```

### Case-1 Res Clang
```
case1.c:5:24: runtime error: unsigned integer overflow: 0 - 1 cannot be represented in type 'unsigned int'
4294967295
检测出unsigned integer overflow。
```