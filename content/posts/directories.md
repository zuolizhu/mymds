---
title: "Directories"
date: 2017-12-13T01:41:29-05:00
draft: true
---

I knew its called folder when I was young.

---

#### First, The Unix File System

- A disk is divided into partitions
- Each partition is a **"file system"**.
  - Each "file system" contains:
    1. A **boot block** (Cotains boostrap code)
    2. A **super block** (Contains infoabout the state of the file system *(e.g. The size of the file system, info of free blocks)*)
    3. A sequence of **[Inodes][inode]**
    4. A sequence of **data blocks** (*Where the actual data (file contents) is stored*)



#### inode

- Contains Information for the stat structure (ownership, size, permissions …)
- 12 direct pointers to data blocks ([Direct Blocks][DirectBlocks])
- One, two, and three level indirect pointers to blocks
  - Pointers to blocks are used to avoid fragmentation



#### DirectBlocks

- Addresses up to 2048 (Typical block sizes are 1024, 2048, and 4096)
  - Assuming a 32-bit architecture (**4** bytes words) and **8192** bits block size
    - Number of addressable blocks = **8192 / 4** = 2048
- Maximum possible file size that can be addressed = 
  -  **Direct data blocks** = 96KB
    - Block size x # of direct pointers to data blocks in Inode
      - 8,192 x 12 = 98,304 bytes = 96Kb
  -  **First-indirect linking** = 16.09MB
    - Block size x # of addresses + Direct data blocks
      - 8,192 x 2,048 + 96Kb = 16,384 Kb + 96Kb = 16,480 Kb = 16.09 Mb
  -  **Double indirect linking** = 32960MB
    - 16,875,520 x 2,048 addressable blocks = 32,960 Mb
  - **Triple indirect linking** = 64.375TB

---

#### Directory

**Directories** in Unix are **files**, but they are not used like ordinary files.

- Directory entries are of variable length.
- Each Directory entry contains:
  - Inode number
  - the length of file name (1 byte)
  - the actual file name (string, may up to 256 characters)



The Directory struct

- Each directory entry is of type `struct dirent` with two data members

```c
#include <dirent.h>
struct dirent {
  ino_t d_ino; // Inode number
  char d_name[]; // File name
}
```

Note:

- If the value of `d_ino` is 0, then the entry does not correspond to a valid file.
- String `d_name is` null terminated.



---

#### Directory Related System Calls

- System call **`mkdir`**   ` int mkdir (const char *pathname, mode_t mode)`

  - Creates a directory with name given by pathname
  - The permission bits for the created directory combine mode with `umask`
  - The created directory is initialized with two entries: **"."** and **".."**.

  ​

- System call **`opendir`**   `DIR *opendir (const char *pathname)`

  - Open a specified directory file and `mallocs` one struct dirent
  - Return a pointer of type DIR * or NULL if an error occurs

  ​

- System call **`closedir`**   ` int closedir (DIR *dirptr)`

  - Close a directory specified by the pointer and `free` the struct dirent
  - Returns 0 if successful and -1 otherwise

  ​

- System Call **`readdir`**   ` struct dirent * readdir (DIR *dirptr)`

  - Reads the next entry from the directory specified by the parameter
  - Returns a pointer to this entry
    - The pointer is of type `struct dirent *`.
    - The return value is NULL if an error occurs or when there are no more entries in the directory
  - Example: Reading entries and outputting the contents of a directory

  ​

- System Call **`rewinddir`**   `void rewinddir (DIR *dirptr) `

  - Goes back to the beginning of the directory specified by the parameter
  - The next call to readdir will return the first entry in the directory

```c
DIR *dp;
struct dirent *d;

// Open the current working directory
if ((dp = opendir(".")) == NULL) {
  perror("opendir"); exit(1);
}

while((d = readdir(dp)) != NULL) {
  printf("%s\n", d->d_name);
}

printf("Second pass on '%s' : \n", dirname);

rewinddir(dir);

while((d = readdir(dp)) != NULL) {
  printf("%s\n", d->d_name);
}

if (closedir(dp) != 0)
  perror("Unable to close directory");
```





- System call **`rmdir`**   `int rmdir (const char *pathname)`

  - Removes the specified directory

  Note: A directory is removed only if it is empty (i.e., the only entries in the directory are for "." and "..")

  ​

- System call **`getcwd`**   `char *getcwd (char *dname, size_t size);`

  - Returns a pointer to the current directory path name
    - The path name is also copied into the array dname
    - Array dname should have size at least one more than the value of size



- System call **`chdir`**   ` int chdir (const char *path)`
  - Changes the working directory to the one specified by the parameter path
  - Returns -1 if the parameter is not a valid directory or the user does not have execute permission for the directory

```c
#include <unistd.h>
#include <stdio.h>
#include <errno.h>

void main() {
  DIR *dp;
  struct dirent *d;
  char cwd[1024];
}

// Get cwd
if (getcwd(cwd, sizeof(cwd)) != NULL)
  fprintf(stdout, "CWD is : %s\n", cwd);
else
  perror("getcwd()");

// Open cwd
if ((dp = opendir(cwd)) == NULL) {
  perror("opendir()"); exit(1);
}

// Change dir
if (chdir(strcat(cwd, "/..")) == -1) {
  perror("opendir()"); eixt(1);
}

// As alway, close dir
```



- System Call **`ftw`**   `int ftw (const char *path, int (*func)(), int depth)`

  - It has own header `<ftw.h>`

  - Performs a (recursive) traversal of the directory tree rooted at the path name given by path

    - By default, directories are handled before the files and subdirectories they contain

  - Input argument depth represents a limit on the number of file descriptors that ftw can use

    - The value of depth can’t be too large
    - Setting it to 1 may be too slow

  - func represents a user defined function

    - For each file visited during the traversal, the user defined function will be called

  - User defined function **`func`**   

    `int func (const char *name, const struct stat *sptr, int type)`

    - name = filename
    - sptr = pointer to the stat structure for the file
    - type can take any of the following values
      - FTW_F (fpath is a regular file)
      - FTW_D (fpath is a directory)
      - FTW_DNR (fpath is a directory which can't be read)
      - FTW_SL (fpath is a symbolic link)
      - FTW_NS (The stat call failed on fpath, which is not a symbolic link)
      - `man ftw` for more options

  - The tree traversal continues if the user defined function returns 0; otherwise ftw terminates the traversal 



```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <ftw.h>

/* 
 * Use the ftw system call to print the name permission bits of
 * each file/directory encountered during the recursive tree walk
*/
#define MODE_MASK 0777
int main(int argc, char *argv[]) {
  int list (const char *, const struct stat *, int);
  if (argc == 1)
    ftw(".", list, 1);
  else
    ftw(argv[1], list, 1);
  return 0;
}
```



---

#### Files, Links and Inodes

Files store information on secondary storage, data is not lost even if the computer is powered off.

- 3 main **components** of a file
  - **Bytes** of the file itself
  - **Metadata** of the file
  - **Links** to the file that are relative to a directory


- When a file is created
  - A "link" is attached to an inode
  - Links are stored in directories


- **You cannot create links to directories**, use "soft" links instead


- Some links are **automatically created** by the system
  - e.g.  for directory dir there are two links: **"dir"** and **"dir/."**
- If  we make a subdirectory of dir, there will be **3 links to dir**
  - "dir", "dir/." and **"dir/sub/.."**



---

#### Permission Bits for Directories

- **Read**: Allows a user to list the files in the directory
- **Write**: Allows a user to create new files and delete files in the
- **Execute**: Allows a user to cd to the directory
- Sticky Bit
  - If the sticky bit for a directory is set, then a file in the directory can be removed or renamed by a normal user only when the user owns the file
  - Some directories (e.g. /tmp) allow any user to create files
    - The permission bits for such directories are read, write and execute for everyone
    - A normal user should not be able to delete or rename files owned by others

