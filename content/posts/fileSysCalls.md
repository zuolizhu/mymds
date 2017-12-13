---
title: "FileSysCalls"
date: 2017-12-13T13:05:43-05:00
draft: true
---

Things goes crazy when you go deeper with file read and write.

---

When read/write from/to a file

- Memory is divided into user space and **kernel** space
  - Kernel
    - Every read and write is executed by the kernel
    - Constantly resides in memory
    - Controls and monitors processes and file accesses
    - User processes request kernel services through **system calls**



---

#### System calls

- Programming interface to the services provided by the OS
  - interface provided to applications to get services
- A simple program may make a lot of calls per second



- A **number** is associated with each system call
  - Used as an index to a System Calls table
- Table keeps addresses of system calls
- System call runs and returns
- All a programmer needs to know to call them is the interface
  - System calls typically **not be accessed directly by programs**, Instead via a **library**.
- The  **library`<stdio.h>`** is built on top of system calls for file access.



System programs provide a convenient environment for program development and execution.

- Some are **simply user interfaces** to system calls
  - Create file: simple system program that just calls the create system call
- Others are considerably **more complex**
  - Compiler



Some system programs

- File manipulation and modification (create, delete, copy, rename, print, text editors)
- Status information (date, time, amount of available memory, disk space, who is logged on)
- Programming language support (compiler, debuggers)
- Program loading and execution (loaders, linkers)
- Communication (ftp, browsers, ssh)

