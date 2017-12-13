---
title: "FileAccessPrimitives"
date: 2017-12-13T12:44:45-05:00
draft: true
---

---

#### Absolute/Relative Path Names

- Absolute path names
  - Path always starts from the root (`/home2/user/public/desktop/README.MD`)
- Relative path names
  - Does not necessarily begin with a slash character (/)
  - Specifies location relative to the current working directory (`desktop/README.MD`)
  - Home-relative path names **with tilde(~)** (`~/file.c` refers to `/home2/user/public/desktop/file.c`)



---

#### File Access and Protection Bits

- 3 classes of users
  1. User(or Owner)
  2. Group
  3. Others
- 3 bits, denoted by rwx for each class.
- Permission is ON(OFF) if the bit is 1(0).
- Using the ls command with the -l flag displays information related to file permission



- Permission bits are usually specified in octal
  - Suppose the permission bits are specified as `754 `(octal)
    - The user (owner) can read, write and execute the file
    - People in the same group can read and execute the file but cannot write to (i.e., modify) the file
    - Others can read the file but cannot write to it or execute it
  - 7: 4 (read) + 2 (write) + 1 (execute)
  - 5: 4 (read) + 1 (execute)
  - 4: 4 (read)

---

#### Changing Permissions

Protection bits can be changed using the **`chmod`** command

- In octal: `chmod 755 file.c`
- In symbolic: `chmod u-x file.c`   `chmod g=rx file.c`   `chmod o+wx file.c`



---

#### Bit Vectors

File access permissions are implemented using bit arrays or bit strings, each bit represents an element of the set.

1 (in the set), 0 (not in the set)



Bitwise Operators

- Shift

  - ` i = 6; /* 0000 0000 0000 0110 */`
  - `j = i << 2; /* 0000 0000 0001 1000 */`
  - `k = i >> 2; /* 0000 0000 0000 0001 */`

- Set

  - `j = 10; /* to set bit at index 10 (start indexing at 0) */`
  - ` i = 1 << j; /* 0000 0100 0000 0000 */`

- Complement (~)

- AND (&)

- OR (|)

- XOR (^)

  ```c
  i = 17; /* 0001 0001 */ 
  j = 3; /* 0000 0011 */ 
  k = ~j; /* 1111 1100 */ 
  m = i & j; /* 0000 0001 */ 
  n = i | j; /* 0001 0011 */ 
  o = i ^ j; /* 0001 0010 */
  ```

- Idioms

  - Setting a bit string to all 1s
    - ` i = ~0;`  `i = -1;`
  - Setting bit j
    - `x = 1 << j;`



---

#### File Descriptors

- Type: `int`

- File descriptors 0, 1 and 2 correspond to `stdin`, `stdout` and `stderr`.

- Use the symbolic names of these devices (`STDIN_FILENO`, `STDOUT_FILENO` and `STDERR_FILENO`) in programs

  Note: File descriptors are different from file pointers (`FILE *`) used in `<stdio.h>`



