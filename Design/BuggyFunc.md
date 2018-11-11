# Module 1 : Buggy Function 

In this module, our main goals are:
1) check the existence of buggy function, if not exist, we could then tell that the vulnerability no longer exists.
2) If the buggy function still exists, but the constraints inside the buggy function always break the vulnerability condition, we could also say this version does not have such vulnerability.

* Some Definitions:
1) Vulnerability Point: refer to the source code which would cause the crash. (only consider developer code,
if the crash triggered in libc or other shared libraries, we trace back in call trace to get the deepest function written by developers)
2) Vulnerability Condition : in which condition the vulnerability would be triggered.
```aidl
//Example
int buf[100];
buf[x] = 1;

Vulnerability Condition : x < 0 or x >= 100
```
3) Vulnerable variable : refer to the variable, the read/write operations to which would trigger vulnerability.
```aidl
//Example

the variable 'buf' in above example.
```

## Meeting 11.11 Preliminary Design

---
* Step 1:
1) Run Poc on Reference Version

                    ---> to get the name of buggy function
                    ---> to get the name of vulnerable buf(only focus on stack overflow temporarily, got from asan output directly)

* Remaining Questions of Step 1:
1) how to accurately locate the vulnerable buf if a single line of source code reads/writes several bufs.
    
    (Since Asan's predict on the name of vulnerable buf is sometimes not reliable)

    <font color="red" >not figure out the possible solution yet </font>

---
* Step 2:
1) Solution of DJR & TX :
    
    1) check the existence of buggy function based on LLVM IR (simply by function name)
    2) if exist, further locate all read/write operations related to the vulnerable buf we got from step.1, still work on LLVM IR
    3)  generate all constraints for these read/write operations.
    (Since we only consider stack overflow temporarily, all constraints would all about the index of stack buf)
    4) if for all these operations, the vulnerability condition could never be satisfied. We could say that the vulnerability not exist in this test version.
2) Solution of ZXH:
    1) check the existence of buggy function based on LLVM IR (simply by function name)
    2) further collect the vulnerability point in Step.1, try to locate the point in test version by matching the featured behavior in CFG, maybe along with the context info.
    
    <font color='red'> Solution Details : FIX ME @ZXH</font>

---

