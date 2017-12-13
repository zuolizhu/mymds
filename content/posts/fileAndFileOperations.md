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





---

#### File Access

File Access Types:

- Both for sequaltial and random access
  - fopen, fclose, fread, fwrite
- Random access only
  - fseek, ftell



Random access:

​	Access time is independent of position

​	Fast access

​	e.g. Array



Formatted Files (text files)

​	Can be viewed and edited using text editor

​	Using `fprintf()` write formatted file

​	Note: The size(number of bytes) of file depends on the value of the integer

Unformatted Files (binary files)

​	Can be viewed and edited using text editor

​	Using `fwrite()` write unformatted file

```c
int num = -2017;
FILE *out_f1, *out_f2;

// After open

fprintf(out_f1, "%d", num);

fwrite((const void *) &num, sizeof(num), 1, out_f2);

// And close
```

formatted (text) file `out1`, **size = 5 bytes**, can be read using `fscanf`.

unformatted (binary) file `out2`, **size = 4 bytes**, can be read using `fread`.



---

Create unformatted (binary) database file 

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define NEMP 100
#define NAME_MAX 20
#define DB_NAME "emp_db.bin"
struct employee{
  char name[NAME_MAX];
  int empid;
  float salary;
};
typedef struct employee EmpRec;
  /* This program creates a file (database) that can */
  /* hold information for up to NEMP employees. */
  /* The initial database contains only "dummy" records */
  /* with empid = -1. */
  /* The name of the file containing the database is */
  /* given by DB_NAME. */
int main(void) {
  FILE *dbf; /* Employee database (output file) */
  EmpRec dummy; /* Dummy record. */
  int i; /* Loop index. */
  /* Initialize dummy record. */
  dummy.empid = -1; strcpy(dummy.name, "");
  dummy.salary = 0.0;
  /* Open the database file. */
  if ((dbf = fopen(DB_NAME, "w")) == NULL) {
    fprintf(stderr, "Could not open file: %s\n", DB_NAME);
    exit(1);
  }
  /* Write NEMP dummy records. */
  for (i = 0; i < NEMP; i++)
  	fwrite((const void *) &dummy, sizeof(EmpRec), 1, dbf);
  /* Close the database file. */
  if (fclose(dbf) == EOF) {
  	fprintf(stderr, "Could not close file: \n", DB_NAME);
  }
  return 0;
} /* End of main. */
```



Updating records in a database (random access) file

```c
#include <stdio.h>
#include <stdlib.h>
#define NEMP 100
#define NAME_MAX 20
#define DB_NAME "emp_db.bin"
struct employee{
  char name[NAME_MAX];
  int empid;
  float salary;
};
typedef struct employee EmpRec;
/* This program asks user to input all the information about */
/* an employee and writes the record for that employee into */
/* the database specified by DB_NAME. */
/* If there is a previous record for the employee, that */
/* record is overwritten. */
/* Recall that all "dummy" records have empid = -1. */
int main(void) {
  FILE *dbf; /* Employee database */
  EmpRec e; /* Temporary record. */
  int i; /* Temporary index. */
  void update_db(FILE *, EmpRec*); /* Prototype */
  /* Open the database file. We need the mode "r+" to */
  /* allow updates. */
  if ((dbf = fopen(DB_NAME, "r+")) == NULL) {
    fprintf(stderr, "Could not open file: %s\n", DB_NAME);
    exit(1);
  }
  /* Obtain employee information and update file until */
  /* an employee id of -1 is typed. */
  /* Employee ids are assumed to be in integers in the range */
  /* 0 through NEMP-1. */
  /* NOTE: The program does not check for input errors. */
  do {
    	printf("Type empid, name and salary: "); fflush(stdout);
    	scanf("%d%s%f", &e.empid, e.name, &e.salary);
    if (e.empid != -1) {
      update_db(dbf, &e);
    }
  } while (e.empid != -1);
  /* All updates have been done. Close the database file. */
  if (fclose(dbf) == EOF) {
  	fprintf(stderr, "Could not close file: \n", DB_NAME);
  }
  return 0;
} /* End of main. */
void update_db(FILE *dbf, EmpRec *eptr) {
/* Updates the database file by writing the information */
/* given by employee record *eptr. */
/* Reach the appropriate offset in the file number using */
/* eptr->empid. */
	fseek(dbf, (eptr->empid)*sizeof(EmpRec), SEEK_SET);
/* Write the information given by the record *eptr. */
/* (Previous record, if any, is lost.) */
	fwrite((const void *) eptr, sizeof(EmpRec), 1, dbf);
} /* End of update_db */
```



Reading records from a database (random access) file

```c
#include <stdio.h>
#include <stdlib.h>
#define NEMP 100
#define NAME_MAX 20
#define DB_NAME "emp_db.bin"
struct employee{
  char name[NAME_MAX];
  int empid;
  float salary;
};
typedef struct employee EmpRec;
/* This program reads a file (database) that holds */
/* information for up to NEMP employees. */
/* The name of the file containing the database is */
/* given by DB_NAME. */
/* Employee records that are valid (i.e., whose empid */
/* field values are not -1) are printed to stdout. */
int main(void) {
  FILE *dbf; /* Employee database (input file) */
  EmpRec e; /* Temporary record. */
  int i; /* Loop index. */
  /* Open the database file. */
  if ((dbf = fopen(DB_NAME, "r")) == NULL) {
    fprintf(stderr, "Could not open file: %s\n", DB_NAME);
    exit(1);
  }
  /* Read each record and output those that are valid records. */
  for (i = 0; i < NEMP; i++) {
  	fread((void *) &e, sizeof(EmpRec), 1, dbf);
    if (e.empid != -1) { /* Valid record */
      printf("%d %s %f\n", e.empid, e.name, e.salary);
    }
  }
  /* Close the database file. */
  if (fclose(dbf) == EOF) {
  	fprintf(stderr, "Could not close file: \n", DB_NAME);
  }
  return 0;
} /* End of main. */
```

