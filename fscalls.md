# File system syscalls

- Note: More read and write fs instructions will be added in the future for ease of use (Read all, write X bytecount)

- Another note: The `f` macro will be used a lot here, so learn how it works first!

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

### REAB : `1 char` for the file slot, `1 short` for how many bytes to read.
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

### SIZE : 1 null terminated `string` for the filename.
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

### LSSZ : 1 null terminated `string` for the filename.
Get the amount of files in a directory as a `char`.


```c
//Example code with syslib.h
int count = fslist(name, currentDir);

//Example code with only lib.h
f(syssend, "LSSZ");
syssend(name);
sysend();
int count = read_char();
```

### GTLS : `1 char` for the index.
Index the current directory. Designed to be used with LSSZ to list a directory's files.
Returns: null terminated string with filetype + filename
examples: `Fim_a_file.txt` - `Dim_a_directory`

```c
//Example code with syslib.h
fsgetindex(currentDir, x, filename, DIR_SIZE);

//Example code with only lib.h
f(syssend, "GTLS");
write_char(index);
syssend(currentDir);
sysend();
sysread(out, size);
```

### EXEC : 1 null terminated `string` for filename.
Executes given filename with the bfk.lua Brainfuck interpreter the OS uses.

```c
//Example code with syslib.h
execInDir(currentDir, command);

//Example code with only lib.h
f(syssend, "EXEC");
syssend(filename);
sysend();
```

### MDIR : 1 null terminated `string` for path name.
Creates the given path.

```c
//Example code with syslib.h
makeDir(currentDir, dirName);

//Example code with only lib.h
f(syssend, "MDIR");
syssend(path);
sysend();
```

### DELF : 1 null terminated `string` for path name.
Deletes the given path.

```c
//Example code with syslib.h
deletePath(currentDir, dirName);

//Example code with only lib.h
f(syssend, "DELF");
syssend(path);
sysend();
```

### WRTE : 1 `char` for fileslot, null terminated `string` for what to write
Writes a string to the given fileslot

```c
//Example code with syslib.h
fswrite(file, text);

//Example code with only lib.h
f(syssend, "WRTE");
write_char(fileslot);
syssend(string);
sysend();
```

### EDIR : null terminated `string` for path
Check if path exists and if it's a directory, returns either 0 or 1

```c
//Example code with syslib.h
bool exists = fsexists(name, currentDir);

//Example code with only lib.h
f(syssend, "EDIR");
syssend(path);
sysend();
bool exists = (read_char() == 1);
```

### EFIL : null terminated `string` for path
Check if path exists and if it's a file, returns either 0 or 1

```c
//Example code with syslib.h
bool exists = fsexistsfile(name, currentDir);

//Example code with only lib.h
f(syssend, "EFIL");
syssend(path);
sysend();
bool exists = (read_char() == 1);
```

### FCPY : `1 char`, size of the source filename, `string` for source filename, null terminated `string` for the destination filename.
Copies a file from a source path to a destination path

```
//Example code with syslib.h
fscopy(currentDir, fileA, fileB);

//Example code with only lib.h
f(syssend, "FCPY");
int i=0;
while (fileA[i]!=0) {
    i = i + 1;
}
write_char(i);
syssend(currentDir);
syssend(fileA);
syssend(currentDir);
syssend(fileB);
sysend();
```

### FMOV : `1 char`, size of the source filename, `string` for source filename, null terminated `string` for the destination filename.
Moves a file from a source path to a destination path

```
//Example code with syslib.h
fsmove(currentDir, fileA, fileB);

//Example code with only lib.h
f(syssend, "FMOV");
int i=0;
while (fileA[i]!=0) {
    i = i + 1;
}
write_char(i);
syssend(currentDir);
syssend(fileA);
syssend(currentDir);
syssend(fileB);
sysend();
```
