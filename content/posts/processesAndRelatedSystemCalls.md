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

  ​