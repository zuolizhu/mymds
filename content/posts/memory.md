---
title: "Memory"
date: 2017-12-13T01:10:08-05:00
draft: true
---

More detail stuff than the plastic stick.

---

Basic concepts of the **memory**:

- A huge array with tons of elements (0xFFFFFFFF).
- Index starting with zero.



Its relation with **pointer**:

- An index to this array
  - e.g. a pointer to 0xEFFFE034 points to 0xEFFFE035th element in the memory array
- Note: not all memory element are accessible
  - e.g. NULL pointer (Dereferencing a NULL pointer results in segmentation fault)



"Legal" memory regions:

- Program instructions (or code)

- Global variables

- **Heap** (memory allocaed(`malloc())` at run time)

- **Stack** (Local variables and procedure arguments are stored)

  Notes:

  - Heap **goes down** with more `malloc()`calls
  - Stack **goes up** with nested procedure calls (e.g. recursion) 



Relation with inodes:

- Memory is broken up into 8,192-byte **pages** (or chunks)

- The OS allocates certain pages of memory for the user

  - Protection of memory required to ensure correct operation
  - A logical address space is defined by a pair of base and limit registers
  - Whenever reading to or writing from an address in memory, the hardware first checks with the OS
    - If the address belongs to a page allocated for the user it can be accessed
    - Otherwise, segmentation violation

  Note: Paging mechanics are covered in the **Operating Systems** class

---

##### Why use main memory

- Program (or data) must be brought from disk into memory
- CPU can directly access **only** registers and main memory
  - Register access costs one (or less) CPU clock cycle
  - Access to main memory can take many cycles, causing CPU to stall
- Cache sits between main memory and CPU registers
- Memory unit
  - Addresses + read requests
  - Addresses + data + write requests

---

Variable **&etext** and **&end**

- Two external, typeless variables: man etext for more information

  ```c
  extern etext;
  extern end;
  ```

- Their **addresses** point to the end of the code and globals segments respectively

- The heap starts immediately after `&end`

  - End of heap changes with `malloc()` calls

- The stack ends with address `0xEFFFFFFF`

  - The beginning of stack changes with procedure calls a program makes
  - The `limit` command allows to print out or change the default stack size
  - Every page between the end of the heap and the beginning of the stack is void
  - Will generate a **`segmentation fault`** if tried to be accessed



Code below :

- Prints out the addresses of `etext` and `end`.
- Prints a location in the code segment.
- Prints an address in the globals segment.
- Prints an address in the stack.
- Prints another address in the stack.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

extern end;
extern etext;

int I;

main (int argc, char **argv) {
  int i;
  int *ii;
  
  printf("Page size %d\n", getpagesize());
  printf("&etext = 0x%lx\n", (unsigned long int) &etext);
  printf("&end = 0x%lx\n", (unsigned long int) &end);
  
  ii = (int *) malloc(sizeof(int));
  
  printf("main = 0x%lx\n", (unsigned long int) main);
  printf("&I = 0x%lx\n", (unsigned long int) &I);
  printf("&i = 0x%lx\n", (unsigned long int) &i);
  printf("&argc = 0x%lx\n", (unsigned long int) &argc);
  printf("&ii = 0x%lx\n", (unsigned long int) &ii);
  printf("ii = 0x%lx\n", (unsigned long int) ii);
}
```



---

#### Memory Fragmentation

Fragmentation: A program has **allocated memory without using it**.

- External
  - Total memory space exists to satisfy a request, but it is not contiguous
- Internal
  - Allocated memory may be slightly larger than requested
  - The size difference is unused memory
- fragmentation wouldn't be a problem with **unlimited memory**
  - Many programs are running at the same time, may compete for memory



Internal Fragmentation Example:

```c
/* 1.6M allocated */
char *array[100000];
main() {
  int i;
  for (i = 0; i < 100000; i++) array[i] = (char *) malloc(8);
}
/* 50% of the heap is “wasted” */
```



---

#### Memory Management

simple partitioning strategy

- (Un)even partitions
- Any process whose size is less than or equal to a partition size can be loaded into the partition
  - If all partitions are occupied, the operating system can swap a process out of a partition
- Big waste of memory
- A program may be too large to fit anyway



Paging

- Main memory is partition into equal fixed-sized chunks (of relatively small size)
- Each program is also divided into chunks of the same size called pages
- Programs can thus be assigned to available chunks in main memory called frames (or page frames)

Note: a process does not need to occupy a contiguous portion of memory



- Because of swapping, a program may occupy different memory locations during its lifetime
  - **Physical memory references** by a process cannot be fixed
  - Solution: **logical addresses**
- Physical address: physical location in main memory
- **Logical address**: reference to a memory location independent of the physical structure of the memory

Note:  compilers produce code in which all memory references are **logical addresses**.