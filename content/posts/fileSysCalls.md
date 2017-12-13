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


---

#### System Calls for File Access

- Header files:
  - `<unistd.h>`: standard symbolic constants and types
  - `<sys/types.h>`: data types
  - `<sys/stat.h>`: data returned by the stat() function
  - `<fcntl.h>`: file control options
- Basic system calls prototype for file I/O
  - `int open(char *path, int flags [ , int mode ] );`
  - `int close(intfd);`
  - `ssize_t read(int fd, void *buf, size_t count);`
  - `ssize_t write(int fd, const void *buf, size_t count);`
  - `off_t lseek(int fd, off_t offset, int whence);`





- System call `open`

  - Frist form prototype: `int open (const char *name, int oflag)` for opening an **existing** file.

    - `name` denotes the name of the file to be opened
    - `oflag` is the file access method
      - Usually the bitwise-or of some of the constants(defined in `<fcntl.h>`) below:
        - O_RDONLY
        - O_WRONLY
        - O_RDWR
        - O_APPEND
        - O_CREAT
        - O_TRUNC

    ```c
    int fd;
    fd = open("user/desktop/file.c", O_WRONLY | O_TRUNC);
    ```

    ​

  - Second form prototype: `int open (const char *name, int oflag, mode_t mode)` **creating a new** file.

    - `mode` specifies permissions for the new file.
    - Returns file descriptor if successful and -1 otherwise.

    ```c
    int fd;
    fd = open("user/desktop/file.c", ORDWR | O_CREAT, MODE);
    ```





  - There is a more complex form of `open`:


      - Prototype: `int open(const char *pathname, int flags, mode_t mode);`

      - `pathname`: pathname for a file.

      - `flags`: one of the following access modes.


          - O_RDONLY
          - O_WRONLY
          - O_RDWR
          - file creation flags and file status flags can be bitwise-or'd in flags

      - `mode`: specifies the file mode bits be applied when a new file is created.


          - must be supplied when O_CREAT is specified in flags else is ignored.

      - Returns a file descriptor or -1 if an error occurred.


          - The file offset is set to the beginning of the file.

      - The call to open checks whether the specified access flags satisfy the file permissions.

        ```c
        int fd;
        mode_t fmode = S_IRWXU | S_IRGRP | S_IROTH;
        if ((fd = open("file", O_WRONLY, fmode)) == -1) {
          fprintf(stderr, "Error in open\n"); exit(1);
        }
        ```

        ​




- System call `creat`
  - Prototype: `int creat (const char *name, mode_t mode)`
  - Can be used for **creating a new** file (using open is generally preferred).
    - `fd = creat("file", 0644);` are same as `fd = open("file", O_WRONLY | O_CREAT | O_TRUNC, 0644);`
  - Returns file descriptor if successful and -1 otherwise



- System call `close`

  - Prototype: `int close (int filedes);`
  - Used to close the file referred to by the descriptor filedes
  - Returns 0 if successful and -1 otherwise

  Note: Although all open files are automatically closed when a program exits, it is a good idea to close the files explicitly.

  ```c
  if(close(fd) == -1) {
    fprintf(stderr, "Close failed.\n"); exit(1);
  }
  ```

  ​







- System call `read`

  - Prototype: `ssize_t read (int fd, void *buf, size_t n);`
  - Reads n bytes from the file given by the descriptor `fd` into memory starting from the location given by `buf`.
  - The file given by the descriptor `fd` **must be open for reading**.
  - Returns:  
    - the `number of bytes` read. 
    - `0` if EOF occurs before any bytes are read. 
    - `-1` if an error occurs

  ```c
  #define SIZE 25
  int fd;
  int temp;
  char buf[SIZE];

  // Remember to open first, then read.

  if((temp = read(fd, buf, (size_t) SIZE)) == -1) {
    fprintf(stderr, "Error in read.\n"); eixt(1);
  }
  ```

  ​

  ​



- System call `write`

  - Prototype: `ssize_t write (int fd, const void *buf, size_t n);`
  - Writes the contents of n bytes starting from the location given by `buf` into the file given by the descriptor `fd`.
  - The file given by the descriptor `fd` must be open for writing.
  - Returns:
    - the `number of bytes` written
    - `-1` if an error occurs

  ```c
  #define SIZE 25
  int fd;
  int temp;
  char buf[SIZE];

  // Remember to open first, then write.

  temp = write(fd, buf, (size_t) SIZE);

  if (temp == -1) {
    fprintf(stderr, "Write error.\n"); exit(1);
  }
  ```







- System call `lseek`

  - Prototype: `off_t lseek (int fd, off_t offset, int sflag);`

  - `offset` specifies the number of bytes for moving (may be negative)

  - `sflag` can be any of the following:

    - SEEK_SET : offset is relative to the beginning of the file.
    - SEEK_CUR : offset is relative to the current position.
    - SEEK_END : offset is relative to the end of the file.

  - Similar to `fseek` of `<stdio.h>`, except that `lseek` uses a *file descriptor* while `fseek` uses a *file pointer*.

  - The file given by the descriptor `fd` must be open for reading or writing.

  - Returns:

    - the `new position` in the file
    - `-1` if an error occurs

    ```c
    int fd; off_t offset, new_pos;

    // Remember to open first, then lseek.

    new_pos = lseek(fd, offset, SEEK_END);

    if (new_pos == -1) {
      fprintf(stderr, "lseek error.\n"); exit(1);
    }
    ```







- System call `unlink` and `remove`
  - Prototypes: 
    - `int unlink (const char *pathname); `
      - Deletes a `name` and possibly the file it refers to.
    - `int remove (const char *pathname);`
      - Deletes a `name` from the file system
      - It calls `unlink` for files, and `rmdir` for directories.
  - Both eliminate the file specified by `pathname`.
  - <Originally, only unlink was in the list of system calls, ANSI C standard added remove>
  - Returns 0 if successful and -1 otherwise and errno is set appropriately.


---

#### Reporting Errors

When a system call reports error, to be known waht the error is:

- Integer variable `errno` in `<errno.h>`

  - a set by system calls and some library functions in the event of an error to indicate what went wrong, can determine the error given the value of errno.

  - Valid error numbers are all **nonzero**.

    Note: for some system calls and library functions -1 is a valid return 	on success

  - Function `perror` available to print error messages

    - prototype: `void perror (const char *msg);`
    - Prints to stderr the message string along with an error message

  - System assigns a value to `errno` when an error occurs.

  ```c
  int fd;
  if((fp = open("user/nofile", O_RDONLY)) == -1) {
    perror("open"); exit(1);
  }
  ```

Note:

- The value of `errno` is not reset when a system call is successful.
- The value of `errno` must be used **only** when a system call returns a *value indicating error*.

