# File system syscalls

Syscalls for interacting with ComputerCraft's file system. Like CC's file system, all paths are absolute, which is why many syslib functions include a "currentDir" parameter for ease of use.

Opened file handles are stored in a "file slot" table. To interact with most of the functions regarding file manipulation you will need to know the file slot of the file you have opened.

### OPEN : `2 chars` for mode, null terminated `string` for filename.
Returns a file slot number. You can open up to 256 files at once.

The modes are made out of the operation, 'a', 'w', 'r', and if it's in text more or not 't', 'b' (Replicates CraftOS' FS)

```c
//Example code with syslib.h
int file = fsopen(currentDir, filename, 'w', 'b');

//Example code with only lib.h
f(syssend, "OPEN");
write_char('w');
write_char('b');
syssend(currentDir);
syssend(filename);
sysend();
int file = read_char();
```

### CLOS : `1 char` for the file slot.
Closes file slot, freeing it for future use, saving all changes.


```c
//Example code with syslib.h
fsclose(fileslot);

//Example code with only lib.h
f(syssend, "CLOS");
write_char(fileslot);
```

### REAB : `1 char` for the file slot, `1 short` for how many bytes to read
Read bytes from an open file slot.
(Current C implementation doesn't support reading more than 255 chars at once, but the syscall supports reading up to 65536 bytes at once)

```c
//Example code with syslib.h
fsclose(fileslot);

//Example code with only lib.h
int out[size];
f(syssend, "REAB");
write_char(fileslot);
write_short(size);
sysread(out, size);
```

### SIZE : 1 null terminated `string` for the filename
Get the size of a file as a `short`.

```c
//Example code with syslib.h
int size = fssize(currentDir, filename);

//Example code with only lib.h
f(syssend, "SIZE");
syssend(filename);
sysend();
int size = read_short();
```

### LSSZ
### GTLS
### EXEC
### MDIR
### DELF
### WRTE
### EDIR
### EFIL
### FCPY
### FMOV
