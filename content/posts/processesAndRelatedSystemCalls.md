---
title: "ProcessesAndRelatedSystemCalls"
date: 2017-12-11T23:53:17-05:00
draft: true
---

This post is my note for unix system processes.

## What is process

A program in excution

- A process is an **instance of a running program**

- It consists of program code, data, variables, open files (actually file descriptors) and allocated memory space, signal mask and signal dispositions.

- A process has its own stack space for function's variable, function calls and returns.

- Processes can be excuted **concurrently** by the **scheduler**.

- There is a **parent** process when a process is created.

- #### Process ID (pid)

  - It is a **number**
  - PID 0 is **always running** and it is an importatnt process of scheduler.
  - PID 1 is **initialization process** and is **always running** as well, it is also first process to start once system boots up.
  - Process Table (A data structure describing all processes).

- #### Parent Process

  - **Share** with its **chiddren**

    1. Memory
    2. File descriptors
    3. File positions
    4. Signal mask
    5. Signal disposition

  - **Does not share**

    1. Status (Because parent and child excute asynchronously)
    2. Parent must wait on a child

    ​

---

Now let talk about some useful shell commands related to process

Some how like **Task Manager** of windows system

- ps : returns the list of processes that are running

- kill : end one or more processes

  - `kill -s`singnal can be numebr or signal name
  - `kill -l`display a list of signal names

  e.g. `kill -9 1274 1297` to end process with pid 1274 and 1297



---

#### System calls for processes

- To obtain PIDs

  - ```c
    <sys/types.h>
    <unistd.h>
    pid_t getpid(void)
    pid_t getppid(void)
    ```

- type `pid_t` is usually unsigned long

- `getpid` returns the pid of the process, `getppid` return the pid of the **parrent** process

- There is no error code return of these call

  ```c
  #include <stdio.h>
  #include <sys/types.h>
  #include <unistd.h>
  int main(void) {
    printf("PID = %ld\n", getpid());
    printf("PPID = %ld\n", getppid());
    return 0;
  }
  ```

  ---

  #### System call **fork**

- There are two independent processes exist after the fork.

- Each process has its own meory, data and variables.

- The child **inherits it's parent's privileges and resources**

- The hild **competes** with its parent for CPU time

  ```c
  #include <stdio.h>
  #include <sys/types.h>
  #include <unistd.h>

  /* The program prints its own id or if it’s a child process, prints the id of its parent process as well */
  int main(void) {
    pid_t child;
    if ((child = fork()) == 0) {
      printf("Child: PID of child = %ld\n", (long)getpid());
    } else {
      if (child == (pid_t)(-1)) {
        fprintf(stderr, "Fork failed.\n");
        exit(1);
      } else {
        printf("Parent: PID of child = %ld\n", (long) child);
        printf("Parent: PID of parent = %ld\n", (long)getpid());
      }
    }
    return 0;
  }
  ```

  Note: 

  - The output produced by the parent and child processes may be **interleaved** because of **Waiting for Processes**, so keep track of the children. 
  - When a child terminates, it’s **still associated** to its parent (child process entry is not freed up, it remains until **parent terminated** or calls **wait**), and it becomes a **zombie process** (kill **does not work** for zombie processes)



- In other words, **zombie process** is a process that has **terminated before** its parent had a chance to wait for it

  ```c
  if ((cid) = fork() == 0) {
    // Code for child
  } else {
    // Code for parent
    c = wait(&status);
  }
  ```

  Above code might have the problem that, child may exit before parent reaches the wait call

  ​

---

#### Waiting for a Process

- Sometimes a parent needs to wait for a child to complete

  - for example: the shell
    - Each shell command (excepted `cd`) is excuted by a child process of shell

  Lets add a little code to fork example with `wait`.

  ```c
  #include <stdio.h>
  #include <sys/types.h>
  #include <unistd.h>

  int main(void) {
    pid_t child;
    /* Add variables for wait */
    int cstatus; // Exit status of child
    pid_t c; // Child's pid to be returned by the wait system call
    
    if ((child = fork()) == 0) {
      printf("Child: PID of child = %ld\n", (long)getpid());
    } else {
      if (child == (pid_t)(-1)) {
        fprintf(stderr, "Fork failed.\n");
        exit(1);
      } else {
        /* call wait */
        c = wait(&cstatus);
        printf("Parent: PID of child = %ld\n", (long) child);
        printf("Parent: PID of parent = %ld\n", (long)getpid());
        /* show child pid and status */
        printf("Parent: Child %ld exited with status = %d\n", (long)c, cstatus);
      }
    }
    return 0;
  }
  ```

  ​

---

#### The exec Famlily of system Calls

- Used __in conjunction__ with fork to create processes that execute **different** code
- Child executes an appropriate exec call
  - execl: used when command line arguments are known at compile time and can be passed as a list
  - execv: used to pass command line arguments as an array (similar to argv[])
- Note:
  -  `execlp` has a variable number of arguments
    - NULL indicates the end of the list
  - `exec` functions only return if an error has occurred
    - The return value is -1, and errno is set to indicate the error
    - There is **no return** value unless an error has occurred



Code example:

```c
/* This program will fork a child process to 
	(1) run the cmd "grep test file.txt" (Using execlp)
	(2) execute the ls program with -l as an argument (Using execvp)
*/

// As before, add a little code on fork example
#include <stdio.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <string.h>
int main(void) {
  pid_t child;
  int cstatus; /* Exit status of child. */
  pid_t c; /* Pid of child to be returned by wait. */
  
  /* Run other program code */
  if ((child = fork()) == 0) {
    
    /* Child process. To begin with, it prints its pid. */
    printf("Child: PID of Child = %ld\n", (long) getpid());
    
    /* Child will now execute the grep command. */
    execlp("grep", "grep", "test", "infile.txt", NULL);
    
    /* If the child process reaches this point, then */
    /* execlp must have failed. */
    fprintf(stderr, "Child process could not do execlp.\n");
    exit(1);
  }
  else { 
    /* Parent process. */
    if (child == (pid_t)(-1)) {
    	fprintf(stderr, "Fork failed.\n"); exit(1);
  }
  else {
    c = wait(&cstatus); /* Wait for child to complete. */
    printf("Parent: Child %ld exited with status = %d\n",(long) c, cstatus);
    }
  }
  return 0;
} /* End of main. */
```

