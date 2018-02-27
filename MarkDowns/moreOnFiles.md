---
title: "MoreOnFiles"
date: 2017-12-13T13:54:16-05:00
draft: true
---

Permission is not that simple.

---

#### User and Group IDs

- Each user is given an ID (integer) called `uid`.
  - The special `uid 0` (zero) is given to the super user (root)
- Most system programs use `uid` instead of the user’s login name
- Each user also belongs to at least one group
  - Each group has an ID (integer) called `gid`
- When a file is created, the `uid` and the `gid` of the user who created the file are stored as part of the information regarding the file.



---

#### More on File Permissions

Permissions are usually specified as octal values for user, group and other.

- Permission mode `632` (octal): 
  - 6: 4 (read) + 2 (write), 3: 2 (write) + 1 (execute), 2: 2 (write).
- Symbolic form also possible (constants defined in `<sys/stat.h>`):
  - `S_IRUSR, S_IWUSR, S_IXUSR, S_IRWXU`
  - `S_IRGRP, S_IWGRP, S_IXGRP, S_IRWXG`
  - `S_IROTH, S_IWOTH, S_IXOTH, S_IRWXO`



Permission Bits for Executable Files

- Three extra permission bits
  - Set `uid` on execution (`S_ISUID`): 04000
    - If `S_ISUID` permission for a file is set, the effective `uid` of the process executing the file is the `uid` of the owner of the file (rather than the `uid` of the user who started the execution)
    - The process is given file access privileges of the file owner.
  - Set `gid` on execution (`S_ISGID`): 02000
    - Files created with `gid` set *inherit their group ID from the directory*, not from the effective group ID of the creating process.
    - For a file that does not have the group execution bit (`S_IXGRP`) set, the set group-ID bit indicates mandatory file locking.
  - Save text/Sticky bit (`S_ISVTX`): 01000
    - A file in a directory with sticky bit set can be **renamed** or **deleted** only by the owner of the file, the owner of the directory, and by a privileged process

Example: The passwd Program

- Program used to change a user’s password
- Changing password requires permission to write to a file containing password information
  - Normally, only the root has permission to write to this file
- The executable file for the program is owned by root but has its `S_ISUID` permission set
- When a normal user runs the program, the program is given the effective user id of the owner (root)
  - Entries in the password file can be modified



---

#### File Creation Mask

- mask: 9-bit quantity (actually grouping of bits)
- Controls how file permissions are set for newly created files
- Generally, the value of umask is specified in a start up file such as `.bash_profile`
  - Usual default value: `022`
  - A bit set to `1` means the corresponding initial file permission will be disabled
  - A bit set to `0` means that the corresponding permission will be determined by the program and the system
  - Setting umask to `022` prevents the open system call from creating a file that can be written by anyone other than the owner
- Value can be found using the shell command umask
- The bits in the mask may be changed by invoking the umask command
- Actual mode used for the file is given by
  - `given_mode & ~umask`



- System call `umask`
  - Prototype: `mode_t umask (mode_t newmask)`
  - Changes umask to the value specified by the parameter and returns the old value of umask





- System call `chmod`

  - Prototype: `int chmod (const char *pathname, mode_t newmode);`
  - Used to change the permission bits of a file
    - Including the special permission bits for executable files
  - The input arguments represent the file name and the new permission bits respectively
  - The call succeeds only when issued by the owner of the file or the superuser (root)
  - Returns 0 if successful and -1 otherwise

  Note: The value of umask is not used by chmod



- System call `chown` 
  - Once the owner is changed, the change cannot be undone by the previous owner
  - Prototype: `int chown (const char *pathname, uid_t newowner, gid_t newgroup)`
  - Input arguments
    - File name
    - `uid` of the new owner
    - `gid `of the new group respectively
  - If the newowner (similarly for newgroup) parameter is -1, the owner is not changed
  - The call succeeds only when issued by the owner of the file or the
    superuser (root); otherwise, errno is set to `EPERM`
  - Returns 0 if successful and -1 otherwise



---

#### Hard and Soft (Symbolic) Links

- Link: mechanism that allows a file to be referred to by different names
- Notes:
  - There is **only one** copy of the file.
  - All links refer to the same copy
  - If a file is deleted, the symbolic links for the file become dangling pointers
  - Shell command for creating a hard link
    - `%> ln oldfile secondname`
  - Shell command for creating a symbolic link
    - `%> ln -s oldfile anothername`





- Files are represented by inodes in the file system
- A file in the file system is basically a link to an inode
- A hard link just creates another file with a link to the same underlying inode
- When a file is deleted one link to the underlying inode is removed
  - The inode is only deleted (released) when all links to the inode have been deleted
- A symbolic link is a link to another name in the file system
  - A symbolic link is a special file.
  - The OS recognizes this file’s data as a path, and redirects open, read, and write requests.
  - Instead of accessing the data within the special file, these functions access the data in the file named by the data in the special file.



Hard and Soft Links Example:

- Create two files

  - `$> touch blah1`
  - `$> touch blah2`

- Enter some data

  - `$> echo “cat” > blah1`
  - `$> echo “dog” > blah2`

- Check the contents

  - `$cat blah1; cat blah2`

    Cat

    Dog

- Create hard and soft links

  - `$> ln blah1 blah1-hard`
  - `$> ln -s blah2 blah2-soft`

- Check the results

  - `$> ls -l`

    blah1

    blah1-hard

    blah2

    blah2-soft -> blah2

- Changing the name of blah1 **does not matter**

  - `$> mv blah1 blah1-new`

  - `$> cat blah1-hard`

    cat

- Changing the name of blah2 **does matter**

  - `$ mv blah2 blah2-new`

  - `$ ls blah2-soft`

    blah2-soft

  - `$ cat blah2-soft `

    cat: blah2-soft: No such file or directory



Differences between Hard and Soft Links

- Hard Link
  - A user **cannot** create a hard link to a directory
  - A hard link must be to a file **within** the same file system
  - The number of hard links to a file is **stored** as part of the information regarding the file
- Soft Link
  - A user **may** create a symbolic link to a directory
  - A soft link may go **across** file systems
  - The system **does not store** information about the number of symbolic links





- System call `link`
  - Creates a hard link to an existing file
  - Prototype: `int link (const char *original_path, const char *new_path)`
  - The parameters represent the name of an existing file and that of the link respectively
  - The link may be in a different directory
  - Returns 0 if successful and -1 otherwise



- System call `simlink`
  - Creates a soft link to an existing file
  - Prototype: `int symlink (const char *real_name, const char *sym_name);`
  - The parameters represent the name of an existing file and that of the symbolic link respectively
  - The link may be in a different directory or even in a differeny file system
  - Returns 0 if successful and -1 otherwise



- System call `unlink`
  - Removes the link named in the call
    - The number of hard links to the file is decremented by 1
  - Prototype: `int unlink(const char *pathname);`
  - The parameter represents the name of an existing file
  - Returns 0 if successful and -1 otherwise
  - The disk space allocated to the file is reclaimed only if both of the following conditions hold:
    - The hard link count has dropped to zero
    - No process has the file open



- System calls `stat`, `fstat` and `lstat`
  - Used to obtain information about files
  - Prototypes:
    - `int stat (const char *pathname, struct stat *buf)`
    - `int fstat (int filedes, struct stat *buf)`
    - `int lstat (const char *pathname, struct stat *buf)`
  - Parameters
    - The first parameter of stat represents the name of an **existing** file
    - The first parameter of fstat represents the file descriptor of an **open** file
    - The first parameter of lstat represents the name of a **symbolic link**
    - buf is a pointer to the memory where information about the file can be stored
      - Space for the buffer must have been allocated before the call
  - Return Value
    - All calls return 0 if successful and -1 otherwise



```c
/* Obtain stat information. */
if (stat(pathname, &statbuf) == -1) {
  fprintf(stderr, "Unable to get stat on file %s\n", pathname); return (-1);
}
```



simply `myls` program without `stat` system call.

```c
main(int arcg, char **argv) {
  int i;
  int fd;
  off_t size;
  
  for(i = 1; i < argc; i++) {
    fd = open(argv[i], O_RDONLY);
    if (fd < 0) {
      printf("Unable to open %s\n", argv[i]);
    }
    else {
      size = lseek(fd, (off_t) 0, SEEK_END);
      printf("%10ld %s \n", size, argv[i]); close(fd);
    }
  }
}
```

Reason use the stat System call:

- `myls` couldn’t print the file size because it couldn’t open the file.
- But `ls` could.
- The `stat` function gives information about a file's inode.
  - It can do this as long as the user has permission to get to the directory that contains the file.

