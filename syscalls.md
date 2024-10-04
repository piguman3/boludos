---
title: Syscalls
layout: page
---

# Syscalls from BrainFuck
## Because why not
The interpreter has various syscalls to access the hardware of the system, because BrainFuck doesn't have the functionality for this.

How a syscall works is, basically:

The BrainFuck code uses the out `.` instruction to output a string of chars with this format to do a system call:
```
[SYSTEMCALL]<params>
```
4 chars for the syscall's name, and then the parameters

For example, to print "Hello world", you do:
```
PRNTHello World\0
```
Not all syscalls end with null chars, only the ones that use strings do this. (SCPX, setCursorPosX, for example, receives a C `short` for its parameter, without any null chars)

To receive the output from a syscall, we repeatedly call the in `,` instruction until we receive all the bytes we want.
For example, to get the output from the `READ` syscall, which returns a null terminated string:
```c
void print(int[] s) {
    int i = 0;
    while(s[i] != 0) {
        write_char(s[i]);
        i = i + 1;
    }
}

void read(int[] s, int size) {
    int r[] = "READ";
    print(r);
    int i = 0;
    int rchar = -1;
    while(rchar != 0 || i < size) {
        rchar = read_char();
        s[i] = rchar;
        i = i + 1;
    }
}

int input[40];
read(input, 40);
```
##### Example written in C, this would need to be compiled with c2bf.
