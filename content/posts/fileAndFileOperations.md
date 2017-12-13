---
title: "FileAndFileOperations"
date: 2017-12-13T11:40:29-05:00
draft: true
---

Files are every where!

---

#### Files

- A collection of data on disk (managed by the user and OS).
- A way to store data **permanently**.
- A file name is how the user and OS know the file follows OS naming rule



- The reason to use files
  - Large volume of I/O data
  - More permanent storage of data
  - Transfer to ther programs
  - Multiple simultaneous input and/or output streams

---

#### Files and File Variables

A C program can only operate directly on variables

​	Need to make **a connect** between the data on this disk and variables in a C program



File Variables

- A file variable is a data structure (FILE in ) which represents a file
- Only exists when program runs (file variable is temporary)



- Type for file variables: `FILE *`
- Pointers to a `FILE struct`
- File operations use functions from `<stdio.h>`
  - `fopen` for opening a file
  - `fclose` for closing a file
  - `getc` for reading characters from a file
  - `putc` for writing characters to a file
  - `fscanf` for reading other data types from a file
  - `fprintf` for writing other data types to files



```C
#include <stdio.h>
#include <stdlib.h>

int main() {
  FILE *fp;			// input file pointer
  long pos;			// position returned by ftell
  int c;			// Read char from input file
  
  if ((fp = fopen("file.txt", "r")) == NULL) {
    fprintf(stderr, "Unable to open file.txt\n"); return(-1);
  }
  
  c = fgetc(fp);
  printf("First char in file is %c \n", c);
  
  // fseek(fp, 5L, SEEK_SET); // offset = 5 from the begining.
  fseek(fp, 0, SEEK_END);
  pos = ftell(fp);
  printf("Size of file.txt = %d bytes\n", pos);
  
  rewind(fp);
  
  if (fclose(fp) == EOF) {
    sprintf(stderr, "Unable to close file file.txt\n");
  }
  
  return 0;
}
```

