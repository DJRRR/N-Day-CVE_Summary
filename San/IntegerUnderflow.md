## Group 1 Integer Underflow

### Case-1 Code
```
void test(){
    unsigned int i = 0;
    unsigned int j = i - 1;
    printf("%u\n",j);
}
```

### Case-1 Res
```
4294967295
同Integer overflow，无warning无报错。
```